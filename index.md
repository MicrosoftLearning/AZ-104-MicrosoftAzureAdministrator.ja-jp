---
title: オンライン ホステッド インストラクション
permalink: index.html
layout: home
ms.openlocfilehash: 1dde36744b9541205d719973757171e13ec37223
ms.sourcegitcommit: 8a0ced6338608682366fb357c69321ba1aee4ab8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "139131807"
---
# <a name="content-directory"></a>コンテンツ ディレクトリ

必要なラボ ファイルは、[こちらからダウンロード](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/archive/master.zip)できます

各ラボの演習とデモへのハイパーリンクを以下に一覧表示します。

## <a name="labs"></a>ラボ

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| モジュール | ラボ |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}


