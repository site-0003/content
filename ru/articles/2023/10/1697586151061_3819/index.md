---
# -------------------------------------------------------------------------------------------------------------------- #
# General settings.
# -------------------------------------------------------------------------------------------------------------------- #

title: 'Создание сертификатов при помощи OpenSSL'
description: ''
images:
  - 'https://images.unsplash.com/photo-1568057373189-8bf0cf6179e6'
categories:
  - 'terminal'
  - 'scripts'
  - 'linux'
tags:
  - 'openssl'
  - 'bash'
  - 'сертификаты'
authors:
  - 'KaiKimera'
sources:
  - ''
license: 'CC-BY-SA-4.0'
complexity: '1'
toc: 1
comments: 1

# -------------------------------------------------------------------------------------------------------------------- #
# Date settings.
# -------------------------------------------------------------------------------------------------------------------- #

date: '2023-10-18T02:42:31+03:00'
publishDate: '2023-10-18T02:42:31+03:00'
expiryDate: ''
lastMod: '2023-10-18T02:42:31+03:00'

# -------------------------------------------------------------------------------------------------------------------- #
# Meta settings.
# -------------------------------------------------------------------------------------------------------------------- #

type: 'articles'
hash: '6733cb5162a00ed9d4218f08c4e0cb1820d36267'
uuid: '6733cb51-62a0-5ed9-b421-8f08c4e0cb18'
slug: '6733cb51-62a0-5ed9-b421-8f08c4e0cb18'

draft: 0
---

{{< tag "OpenSSL" >}} является библиотекой по работе с криптографией, но в этой статье я затрону только часть функций этого инструмента, а именно создание само-подписанных сертификатов.

<!--more-->

## Создание центра сертификации

При помощи корневого сертификата, мы будем подписывать сертификаты клиентские. Создаём сертификат центра сертификации...

```sh
openssl req -x509 \
  -newkey ec:<( openssl ecparam -name 'secp384r1' ) \
  -nodes -days '3650' \
  -keyform 'PEM' -outform 'PEM' \
  -keyout 'ca.root.key' -out 'ca.root.crt'
```

Где:

- `-days '3650'` - количество дней, по прошествии которого сертификат центра сертификации станет недействительным.
- `-keyout 'ca.root.key'` - название создаваемого файла с ключом центра сертификации.
- `-out 'ca.root.crt'` - название создаваемого файла с сертификатом центра сертификации.

## Создание клиентского сертификата

Как только будет готов корневой сертификат, можно приступать к выпуску клиентских сертификатов.

### Создание приватного ключа

Для начала создаём приватный ключ.

```sh
openssl ecparam -name 'prime256v1' -genkey -noout -out 'client.key.private.pem'
```

Где:

- `-out 'client.key.private.pem'` - название создаваемого файла с клиентским приватным ключом.

### Создание публичного ключа

Создание публичного ключа будет не лишним.

```sh
openssl ec -in 'client.key.private.pem' -pubout -out 'client.key.public.pem'
```

Где:

- `-in 'client.key.private.pem'` - файл c клиентским приватным ключом.
- `-out 'client.key.public.pem'` - название создаваемого файла с клиентским публичным ключом.

### Создание запроса на подпись сертификата

Выполняем запрос на сертификат.

```sh
openssl req -new -key 'client.key.private.pem' -out 'client.csr'
```

Где:

- `-key 'client.key.private.pem'` - файл с приватным ключом клиентского сертификата.
- `-out 'client.csr'` - название создаваемого файла с запросом на подпись клиентского сертификата.

При выполнении запроса будет предложено ввести актуальные данные для будущего сертификата:

- `Country Name (2 letter code)` - двухбуквенный код страны, в которой юридически находится ваша организация.
- `State or Province Name (full name)` - штат или провинция, в которой юридически находится ваша организация.
- `Locality Name (e.g., city)` - город, в котором юридически находится ваша организация.
- `Organization Name (e.g., company)` - юридически зарегистрированное название вашей организации.
- `Organizational Unit Name (e.g., section)` - название вашего отдела в организации (опционально).
- `Common Name (e.g., server FQDN)` - полное доменное имя (FQDN) (например, www.example.com).
- `Email Address` - ваш адрес email (опционально).
- `A challenge password` - пароль (опционально).
- `An optional company name` - необязательное название компании (опционально).

Самое главное поле это `Common Name (e.g., server FQDN)` (`CN`), заполнять его необходимо очень внимательно.

### Создание и подпись сертификата

В заключительной части остаётся только создать сам сертификат и подписать его.

```sh
openssl x509 -req -in 'client.csr' \
  -CA 'ca.crt' -CAkey 'ca.key' -days '3650' \
  -outform 'PEM' -out 'client.crt'
```

Где:

- `-in 'client.csr'` - файл с запросом клиентского сертификата.
- `-CA 'ca.crt'` - файл с сертификатом центра сертификации.
- `-CAkey 'ca.key'` - файл с ключом центра сертификации.
- `-days '3650'` - количество дней, по прошествии которого клиентский сертификат станет недействительным.
- `-out 'client.crt'` - название создаваемого файла с клиентским сертификатом.

### Экспорт сертификата

Для того, чтобы импортировать сертификат на клиентские устройства, его необходимо экспортировать в формат `P12`. `P12` является контейнером, в котором содержится приватный ключ сертификата и сам сертификат.

```sh
openssl pkcs12 -export \
  -inkey 'client.key.private.pem' \
  -in 'client.crt' -out 'client.p12'
```

Где:

- `-inkey 'client.key.private.pem'` - файл с приватным клиентским ключом.
- `-in 'client.crt'` - файл с клиентским сертификатом.
- `-out 'client.p12'` - название создаваемого файла-контейнера с приватным ключом и сертификатом.

### Верификация сертификата

```sh
openssl verify -CAfile 'ca.crt' 'client.crt'
```

- `-CAfile 'ca.crt'` - файл с сертификатом центра сертификации.
- `'client.crt'` - файл с клиентским сертификатом.

### Просмотр сертификата

```sh
openssl x509 -in 'client.crt' -text
```

Где:

- `-in 'client.crt'` - файл с клиентским сертификатом.

При выполнении команды, терминал покажет информацию со всеми основными сведениями о сертификате (пример ниже).

```terminal {os="linux"}
openssl x509 -in 'client.crt' -text

Certificate:
    Data:
        Version: 1 (0x0)
        Serial Number:
            49:14:b8:78:17:a1:ca:c4:6a:41:1b:23:f3:8a:8d:36:e0:9e:69:81
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: C = AU, ST = Some-State, O = Internet Widgits Pty Ltd, CN = CA
        Validity
            Not Before: Oct 18 21:31:53 2023 GMT
            Not After : Oct 15 21:31:53 2033 GMT
        Subject: C = AU, ST = Some-State, O = Internet Widgits Pty Ltd, CN = CN-FQDN, emailAddress = mail@example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:9f:22:91:cc:14:b3:75:ad:44:cc:0d:96:0d:19:
                    04:c4:20:79:fd:b7:f5:65:64:dd:65:a1:f0:b8:2d:
                    e4:02:75:62:41:03:7c:6e:2f:48:e4:11:38:dc:ed:
                    08:1f:4c:fa:5f:2f:a2:f5:5d:51:2e:8e:de:de:70:
                    33:aa:e5:3b:95
                ASN1 OID: prime256v1
                NIST CURVE: P-256
    Signature Algorithm: ecdsa-with-SHA256
         30:64:02:30:0d:ff:b1:dc:8b:a6:c6:9c:ad:65:8a:7c:01:41:
         9f:91:ca:24:4c:0b:28:5a:5c:f6:35:2a:b2:d7:58:ca:39:da:
         6c:bf:cf:8b:23:20:ce:11:45:13:61:36:e2:23:4a:e9:02:30:
         54:dc:45:ed:ef:21:58:ea:c7:b6:63:db:a2:d9:71:fe:3d:b3:
         d6:1e:15:82:7b:c8:e8:08:33:d5:2f:d5:f2:8f:3b:41:ea:53:
         1e:2d:a9:1e:9e:25:9c:fb:a7:12:f9:ec
```

### Просмотр запроса на подпись сертификата

```sh
openssl req -in 'client.csr' -text
```

Где:

- `-in 'client.csr'` - файл с запросом на подпись клиентского сертификата.

Команда выдаст информацию по запросу на подпись клиентского сертификата (пример ниже).

```terminal {os="linux"}
openssl req -in 'client.csr' -text

Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: C = AU, ST = Some-State, O = Internet Widgits Pty Ltd, CN = CN-FQDN, emailAddress = mail@example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:9f:22:91:cc:14:b3:75:ad:44:cc:0d:96:0d:19:
                    04:c4:20:79:fd:b7:f5:65:64:dd:65:a1:f0:b8:2d:
                    e4:02:75:62:41:03:7c:6e:2f:48:e4:11:38:dc:ed:
                    08:1f:4c:fa:5f:2f:a2:f5:5d:51:2e:8e:de:de:70:
                    33:aa:e5:3b:95
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        Attributes:
            a0:00
        Requested Extensions:
    Signature Algorithm: ecdsa-with-SHA256
         30:45:02:20:51:9c:a0:b9:b2:61:7f:fa:64:ea:34:1f:65:15:
         37:ae:f4:89:30:47:11:89:db:c4:1b:1d:9b:82:b9:64:ea:c7:
         02:21:00:f8:00:70:4e:e6:db:99:78:cf:25:22:c0:8d:c6:b1:
         f3:f0:d8:68:3b:c2:51:21:a5:b3:fa:97:f9:85:c6:9c:49
```

## Автоматизация

Команд много. Поэтому я как обычно, решил всё автоматизировать и загнать в скрипт.

{{< file "bash.openssl.ca.sh" >}}

Скрипт содержит две функции, вызывать которых можно по отдельности. Функция `ca()` позволяет сгенерировать ключ и сертификат центра сертификации, а функция `cert()`, соответственно, генерирует клиентские сертификаты.

### Использование

1. Создать ключ и сертификат центра сертификации:

```terminal {os="linux"}
bash bash.openssl.ca.sh ca
```

2. Создать клиентские ключи и сертификаты:

```terminal {os="linux"}
bash bash.openssl.ca.sh cert
```

Немного расскажу про алгоритм генерации сертификатов.

1. У сертификата есть поле `Serial Number`, которое содержит серийный номер сертификата. Серийный номер сертификата это уникальный номер, выданный центром сертификации. В моём скрипте при генерации клиентского сертификата указаны опции `-CAcreateserial` и `-CAserial "${srl}"`, которые позволяют OpenSSL создавать рядом с клиентскими сертификатами файлы формата `.slr`, содержащие в себе серийные номера сгенерированных сертификатов. Таким образом можно создавать небольшую локальную базу данных выданных сертификатов.
2. Файл каждого клиентского сертификата генерируется с названием вида `[TS].[SFX].[EXE]`, где `[TS]` - это временная метка в микросекундах, `[SFX]` - числовой суффикс, а `[EXE]` - расширение файлов.

На этом всё. Не исключаю факта присутствия ошибок или неточностей. Если что, жду предложения в электронную почту. {{< emoji ":smile:" >}}
