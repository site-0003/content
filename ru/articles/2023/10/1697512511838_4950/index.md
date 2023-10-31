---
title: 'Невидимые ссылки для гостей на IP.Board 2.3'
description: ''
images:
  - 'https://images.unsplash.com/photo-1597237364955-c094c7171326'
cover:
  crop: 'entropy'
  fit: 'crop'
categories:
  - 'cmf'
  - 'inHistory'
tags:
  - 'ipb'
  - 'ips'
  - 'forum'
authors:
  - 'KitsuneSolar'
sources:
  - ''
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

date: '2023-10-17T06:15:11+03:00'
publishDate: '2023-10-17T06:15:11+03:00'
expiryDate: ''
lastMod: '2023-10-17T06:15:11+03:00'

hash: '560dfed89a251db345a6a7df8129e9600f662b8b'
uuid: '560dfed8-9a25-5db3-95a6-a7df8129e960'
slug: '560dfed8-9a25-5db3-95a6-a7df8129e960'

draft: 1
---

Когда-то была очень полезная модификация, которая защищала ссылки внутри форума от гостей. Ибо нефиг смотреть просто так, регистрируйтесь!

<!--more-->

{{< alert "info" >}}
- Автор: Sannis.
{{< /alert >}}

## Внедрение модификации

Эта небольшая модификация позволяет сделать так, чтобы ссылки стали скрытыми от гостей. Для этого нужно отредактировать файлы `topic.php` и `class_post.php`.

Открыть файл `./sources/action_public/topic.php`, найти:

```php
//-----------------------------------------
// Highlight...
//-----------------------------------------
```


Заменить на:

```php
if (!$this->ipsclass->member['id']) {
  //-----------------------------------------
  // Clear links for guests
  //-----------------------------------------

  $row['post'] = preg_replace("#<a href=[\"'].+?[\"'].+?>.+?</a>#", "<i>ссылка</i>", $row['post']);
}
```

Открыть файл `./sources/classes/class_post.php`, найти:

```php
$extra = "";

if ( $tmp_post )
{
  $raw_post .= "[quote name='".$this->parser->make_quote_safe($tp['author_name'])."' date='".$this->parser->make_quote_safe($this->ipsclass->get_date( $tp['post_date'], 'LONG', 1 ))."' post='".$tp['pid']."']\n$tmp_post\n".$extra.'[/quote]'."\n\n\n";
}
```


Заменить на:

```php
if (!$this->ipsclass->member['id']) {
  //-----------------------------------------
  // Clear links for guests
  //-----------------------------------------

  $tmp_post = preg_replace("#\[url\](\S+?)\[/url\]#i", "[i]ссылка[/i]", $tmp_post);
  $tmp_post = preg_replace("#\[url\s*=\s*\"\;\s*(\S+?)\s*\"\;\s*\](.*?)\[\/url\]#i", "\\2", $tmp_post);
  $tmp_post = preg_replace("#\[url\s*=\s*(\S+?)\s*\](.*?)\[\/url\]#i", "\\2", $tmp_post);
}
```