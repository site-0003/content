---
title: 'Восстановление доверительных отношений между PC и Active Directory'
description: ''
images:
  - 'https://images.unsplash.com/photo-1477383384651-442903132e79'
cover:
  crop: 'entropy'
  fit: 'crop'
categories:
  - 'windows'
  - 'scripts'
  - 'terminal'
tags:
  - 'powershell'
  - 'domain'
  - 'ad'
authors:
  - 'KitsuneSolar'
sources:
  - 'https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/test-computersecurechannel'
  - 'https://learn.microsoft.com/en-us/troubleshoot/azure/virtual-machines/troubleshoot-broken-secure-channel'
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

date: '2023-10-27T12:25:25+03:00'
publishDate: '2023-10-27T12:25:25+03:00'
expiryDate: ''
lastMod: '2023-10-27T12:25:25+03:00'

hash: '38fc94dd8d37ef9e5556676304976a9f80f5d495'
uuid: '38fc94dd-8d37-5f9e-b556-676304976a9f'
slug: '38fc94dd-8d37-5f9e-b556-676304976a9f'

draft: 0
---

Рассмотрим способы решения распространённой проблемы потери доверительных отношений между рабочей станцией и доменом Active Directory. Из-за этой проблемы пользователь не может авторизоваться под своей учётной записью.

<!--more-->

При попытке входа пользователя под доменной учётной записью, могут произойти следующие ошибки.

{{< window title="Trust relationship" type="danger" >}}
The trust relationship between this workstation and the primary domain failed.

(Не удалось восстановить доверительные отношения между рабочей станцией и доменом.)
{{< /window >}}

{{< window title="Security database" type="danger" >}}
The security database on the server does not have a computer account for this workstation trust relationship.

(База данных диспетчера учетных записей на сервере не содержит записи для регистрации компьютера через доверительные отношения с этой рабочей станцией.)
{{< /window >}}

Ошибка может возникнуть по различным факторам. Например, когда новый компьютер присоединился к домену под уже существующем именем другого компьютера, или когда виртуальная машина восстановилась с контрольной точки.

## Ручной способ

Для того, чтобы исправить ошибку, необходимо последовательно выполнить следующие пункты:

1. Сбросить аккаунт компьютера в Active Directory.
2. На проблемном компьютере зайти под локальным администратором и вывести компьютер из домена в рабочую группу.
3. Перезагрузить компьютер.
4. Вывести компьютер из рабочей группы и ввести обратно в домен.
5. Еще раз перезагрузить компьютер.

## Автоматизация

Как обычно, привожу скрипт по автоматизации восстановления доверительных отношений. Скрипт решит проблему быстро и без перезагрузок. Запускается под администратором.

### Параметры

Параметр у скрипта один, это название сервера DC.

- `-S` `-P_Server` - название сервера DC (контроллера домена). Если параметр не задан, скрипт выбирает для операции контроллер домена по умолчанию.

### Примеры

Восстановить доверительные отношения с доменом по умолчанию:

```terminal {os="windows", mode="root"}
.\pwsh.csc.repair.ps1
```

Восстановить доверительные отношения с доменом `DC-server.domain.com`:

```terminal {os="windows", mode="root"}
.\pwsh.csc.repair.ps1 -DC 'DC-server.domain.com'
```

### Алгоритм работы

- Запускается проверка доверительных отношений при помощи cmdlet'а `Test-ComputerSecureChannel`.
- Если проверка пройдена, скрипт завершает работу.
- Если проверка не пройдена, запускается цикл восстановления доверительных отношений с контроллером домена с временным интервалом в 5 секунд.

### Скрипт

{{< file "pwsh.csc.repair.ps1" >}}