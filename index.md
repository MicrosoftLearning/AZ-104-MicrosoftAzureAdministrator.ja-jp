---
title: オンラインでホスティングされている手順
permalink: index.html
layout: home
---

# Notes for logging issues
[README](Instructions/README-Notes.md)

# コンテンツ ディレクトリ

必要なラボ ファイルは、[こちらからダウンロード](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/archive/master.zip)できます

## ラボ

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| モジュール | ラボ |
| --- | --- | 
{% for activity in labs %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

## デモンストレーション

{% assign demos = site.pages | where_exp:"page", "page.url contains '/Instructions/Demos'" %}
| モジュール | デモンストレーション |
| --- | --- | 
{% for activity in demos %}| {{ activity.demo.module }} | [{{ activity.demo.title }}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
