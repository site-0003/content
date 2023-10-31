---
title: 'Генератор идентификаторов расширений'
description: ''
images:
  - 'https://images.unsplash.com/photo-1575470522418-b88b692b8084'
categories:
  - 'cmf'
tags:
  - 'cms'
  - 'cmf'
  - 'php'
authors:
  - 'KitsuneSolar'
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

date: '2020-08-03T00:01:49+03:00'
hash: '5d35edd8f0e35b393fd32ac3b15b8c4f4f156ede'
uuid: '5d35edd8-f0e3-5b39-afd3-2ac3b15b8c4f'
slug: '5d35edd8-f0e3-5b39-afd3-2ac3b15b8c4f'

draft: 0
---

В свободное от работы время я балуюсь разработкой небольших модулей и расширений для различных CMF, и некоторые CMF требуют использовать идентификаторы в расширениях.

<!--more-->

Я стараюсь как то "обезличить" и унифицировать идентификаторы. Поэтому, для генерации идентификаторов использую очень простой BASH-скрипт. В идентификатор кодируется текущее время в формате UNIX с наносекундами.

Пример кодирования:

```sh
echo "ext_$(date +%s%N | sha512sum | fold -w 8 | head -n 1)"
```

Вывод: `ext_96824896`

Если необходимо, можно конвертировать в верхний регистр:

```sh
echo "EXT_$(date +%s%N | sha512sum | fold -w 8 | head -n 1 | tr '[:lower:]' '[:upper:]')"
```

Вывод: `EXT_950E60E1`