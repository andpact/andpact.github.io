---
title: "Shorts"
layout: archive
permalink: categories/Shorts
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.Shorts %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}