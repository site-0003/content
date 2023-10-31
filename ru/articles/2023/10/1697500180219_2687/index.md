---
title: 'Условия в файлах IP.Board 2.3'
description: ''
images:
  - 'https://images.unsplash.com/photo-1535551951406-a19828b0a76b'
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
complexity: '1'
toc: 1
comments: 1

date: '2023-10-17T02:49:40+03:00'
publishDate: '2023-10-17T02:49:40+03:00'
expiryDate: ''
lastMod: '2023-10-17T02:49:40+03:00'

hash: '0743e73e7c4cdab8eba3ba70bdb5cdfdafdb7079'
uuid: '0743e73e-7c4c-5ab8-bba3-ba70bdb5cdfd'
slug: '0743e73e-7c4c-5ab8-bba3-ba70bdb5cdfd'

draft: 0
---

Условия в файлах для **IP.Board 2.3**. Версия **2.3** ужа давно не актуальна, но информация пусть будет.

<!--more-->

## Условия в файлах

1. Показать информацию только для пользователей.

```php
if ( $this->ipsclass->member['id'] )
{
  /* информация только для пользователей */
}
```

2. Показать информацию только супер-модераторам.

```php
if ( $this->ipsclass->member['g_is_supmod'] )
{
  /* информация только для супермодераторов */
}
```

3. Показать информацию только администраторам.

```php
if ( $this->ipsclass->member['mgroup'] == $this->ipsclass->vars['admin_group'] )
{
  /* информация только для администраторов */
}
```

4. Показать информацию только модераторам.

```php
if ( $this->ipsclass->member['_moderator'] )
{
  /* информация только для модераторов */
}
```

5. Показать информацию в определённом форуме.

```php
if ( $this->ipsclass->input['f'] = 35 )
{
  /* информация только в опеределённом форуме с ID = 35 */
}
```

6. Показать информацию на определённой странице.

```php
if ( $ipsclass->input['_low_act'] == 'home' )
{
  /* информация только на определённой странице (в данном случае - home) */
}
```

7. Пример сложной конструкции с применением `<или>`.

```php
if ( $this->ipsclass->member['id'] )
{
  /* информация только для пользователей */
}
else
{
  /* информация только для гостей */
}
```