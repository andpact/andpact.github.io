---
title: "Android 앱 언어 설정: API 32 이하에서 locale을 직접 저장해야 하는 이유"
date: 2026-04-04
layout: single
categories: [android]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Android 앱에 다국어를 지원하다 보면
"앱을 껐다가 다시 켰을 때 선택한 언어가 초기화된다"는 문제를 마주치게 됩니다.

Android 13(API 33)부터는 시스템이 앱별 언어 설정을 직접 관리해주지만,
**API 32 이하에서는 앱이 직접 locale을 저장하고 복원해야 합니다.**

이 글에서는 왜 이런 차이가 생기는지, 그리고 API 32 이하를 포함해
모든 버전에서 언어 설정을 올바르게 유지하는 방법을 정리합니다.

## 문제의 원인

### API 33 이상: 시스템이 저장해준다

Android 13부터 추가된 `LocaleManager`를 사용하면
앱별 언어 설정이 **시스템 수준에서 영구적으로 저장**됩니다.

```kotlin
// API 33 이상
val localeManager = context.getSystemService(LocaleManager::class.java)
localeManager.applicationLocales = LocaleList(Locale.forLanguageTag("ko"))
```

앱을 종료했다가 다시 실행해도 시스템이 알아서 복원해줍니다.
개발자가 별도로 SharedPreferences에 저장할 필요가 없습니다.

### API 32 이하: 앱이 직접 저장해야 한다

API 32 이하에서는 `LocaleManager`가 존재하지 않습니다.
`AppCompatDelegate.setApplicationLocales()`로 언어를 바꿀 수는 있지만,
이 설정은 **앱이 종료되면 사라집니다.**

앱을 다시 시작할 때 저장된 locale을 읽어서 직접 다시 적용해야 합니다.

공식 문서([Per-app language preferences](https://developer.android.com/guide/topics/resources/app-languages))도 이 점을 명시하고 있습니다:

> "API 32 이하에서는 앱이 직접 locale을 SharedPreferences에 저장하고 앱 시작 시 읽어와야 합니다."

## 권장 방법: AppCompatDelegate

Jetpack의 `AppCompatDelegate.setApplicationLocales()`는
API 버전에 따라 동작을 자동으로 다르게 처리합니다.

- **API 33 이상**: 내부적으로 `LocaleManager`를 사용해 시스템에 저장
- **API 32 이하**: 앱 프로세스 내에서만 적용 (재시작 시 사라짐)

이 때문에 API 32 이하를 지원한다면 저장 로직을 직접 구현해야 합니다.

`AppCompatDelegate`를 사용하려면 `appcompat` 1.6.0 이상이 필요합니다.

```kotlin
// build.gradle.kts
dependencies {
    implementation("androidx.appcompat:appcompat:1.6.1")
}
```

## API 32 이하에서 locale 저장하고 복원하기

### 1. 언어 변경 시 SharedPreferences에 저장

사용자가 언어를 선택했을 때 두 가지를 함께 처리합니다.

```kotlin
fun applyLanguage(context: Context, languageTag: String) {
    // 1. SharedPreferences에 저장
    val prefs = context.getSharedPreferences("app_settings", Context.MODE_PRIVATE)
    prefs.edit().putString("selected_locale", languageTag).apply()

    // 2. 즉시 언어 적용 (메인 스레드에서 호출해야 함)
    val localeList = LocaleListCompat.forLanguageTags(languageTag)
    AppCompatDelegate.setApplicationLocales(localeList)
}
```

### 2. 앱 시작 시 저장된 언어 복원

`Application` 클래스의 `onCreate()`에서 저장된 locale을 읽어 적용합니다.
Activity보다 먼저 실행되기 때문에 화면이 그려지기 전에 언어가 설정됩니다.

```kotlin
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        restoreLocale()
    }

    private fun restoreLocale() {
        val prefs = getSharedPreferences("app_settings", Context.MODE_PRIVATE)
        val savedLocale = prefs.getString("selected_locale", "") ?: return

        if (savedLocale.isNotEmpty()) {
            val localeList = LocaleListCompat.forLanguageTags(savedLocale)
            AppCompatDelegate.setApplicationLocales(localeList)
        }
    }
}
```

`AndroidManifest.xml`에 `Application` 클래스를 등록합니다.

```xml
<application
    android:name=".MyApplication"
    ...>
</application>
```

### 3. 시스템 기본 언어로 되돌리기

사용자가 "기기 언어 따르기"를 선택했을 때는
빈 locale 리스트를 설정하고 SharedPreferences에서도 값을 지웁니다.

```kotlin
fun resetToSystemLanguage(context: Context) {
    val prefs = context.getSharedPreferences("app_settings", Context.MODE_PRIVATE)
    prefs.edit().remove("selected_locale").apply()

    AppCompatDelegate.setApplicationLocales(LocaleListCompat.getEmptyLocaleList())
}
```

## autoStoreLocales 옵션

AndroidX는 API 32 이하의 저장 작업을 자동으로 처리하는 옵션을 제공합니다.
`AndroidManifest.xml`에 아래 서비스를 추가하면
AppCompat이 내부적으로 SharedPreferences에 저장하고 복원합니다.

```xml
<application>
    <service
        android:name="androidx.appcompat.app.AppLocalesMetadataHolderService"
        android:enabled="false"
        android:exported="false">
        <meta-data
            android:name="autoStoreLocales"
            android:value="true" />
    </service>
</application>
```

단, 이 방식은 **Android 12 이하에서 메인 스레드 blocking read가 발생할 수 있다**는
주의 사항이 있습니다. 성능이 중요한 앱이라면 위에서 설명한 수동 구현 방식을 권장합니다.

## 현재 적용된 언어 확인

```kotlin
val currentLocales = AppCompatDelegate.getApplicationLocales()

if (currentLocales.isEmpty) {
    // 시스템 언어를 따르는 상태
} else {
    val tag = currentLocales.toLanguageTags() // 예: "ko", "en-US"
}
```

## 정리

| 항목 | API 33 이상 | API 32 이하 |
|------|------------|------------|
| 언어 저장 주체 | 시스템 (`LocaleManager`) | 앱 직접 (SharedPreferences 등) |
| 앱 재시작 후 복원 | 자동 | 수동 복원 필요 |
| 권장 API | `AppCompatDelegate` (내부적으로 `LocaleManager` 사용) | `AppCompatDelegate` + 수동 저장 |

API 32 이하를 지원하는 앱이라면
언어 변경 시 SharedPreferences에 저장하고,
앱 시작 시 `Application.onCreate()`에서 읽어서 적용하는 흐름을 반드시 구현해야 합니다.
`autoStoreLocales` 옵션으로 이 작업을 위임할 수도 있지만, 메인 스레드 blocking 이슈를 감안해 선택하세요.
