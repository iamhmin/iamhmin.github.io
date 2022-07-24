---
title: "next.js"
layout: archive
classes: wide
permalink: categories/next
author_profile: true
sidebar_main: true
---


***

{% assign posts = site.categories.next %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
