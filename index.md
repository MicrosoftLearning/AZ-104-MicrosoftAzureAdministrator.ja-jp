---
title: オンライン ホステッド インストラクション
permalink: index.html
layout: home
---

# コンテンツ ディレクトリ

必要なラボ ファイルは、[こちらからダウンロード](https://github.com/MicrosoftLearning/AZ-104JA-MicrosoftAzureAdministrator/archive/master.zip)できます

各ラボの演習とデモへのハイパーリンクを以下に一覧表示します。

## ラボ

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| モジュール | ラボ |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}


