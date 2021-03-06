---
title: Введение в ASP.NET Core SignalR
author: tdykstra
description: Узнайте, как библиотека ASP.NET Core SignalR упрощает добавление функциональности в режиме реального времени к приложениям.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: bc6f25c3f35e7fb0c2c68220697f2e0fdc6a9958
ms.sourcegitcommit: 3ca527f27c88cfc9d04688db5499e372fbc2c775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39095393"
---
# <a name="introduction-to-aspnet-core-signalr"></a>Введение в ASP.NET Core SignalR

Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)

## <a name="what-is-signalr"></a>Что такое SignalR

ASP.NET Core SignalR — это библиотека, которая упрощает создание новых функций в режиме реального времени к приложениям. В режиме реального времени веб-функций позволяет коду на сервере содержимого Push-уведомлений на клиентах мгновенно.

Хорошими кандидатами для использования SignalR:

* Приложения, требующие высокой частотой обновления с сервера. Примерами являются игры, социальные сети, голосования, аукцион, карт и приложений GPS.
* Панели мониторинга и мониторинга приложения. Примеры включают информационные панели компании, мгновенного обновления продаж, или командировки оповещения.
* Совместной работы приложения. Доска приложения и группы программного обеспечения являются примерами приложений совместной работы.
* Приложения, которым требуются уведомления. Уведомления о использовать социальных сетей, электронной почты, чат, игры, travel оповещения и многие другие приложения.

SignalR предоставляет API для создания клиентом и сервером [удаленные вызовы процедур (RPC)](https://wikipedia.org/wiki/Remote_procedure_call). RPC вызывают функции JavaScript на клиентах из кода .NET Core на сервере.

SignalR для ASP.NET Core:

* Автоматически обрабатывает управление подключениями.
* Включает широковещательная рассылка сообщений для всех подключенных клиентов одновременно. Например в чат.
* Разрешает передачу сообщений для конкретных клиентов или групп клиентов.
* Является открытым в [GitHub](https://github.com/aspnet/signalr).
* Масштабируемость.

Соединение между клиентом и сервером является постоянным, в отличие от HTTP-соединение.

## <a name="transports"></a>Транспорты

Краткие описания SignalR через ряд методов для создания в режиме реального времени веб-приложений. [WebSockets](https://tools.ietf.org/html/rfc7118) является оптимальной транспортом, но можно использовать другие методы, такие как события Server-Sent и длинный опрос при тех недоступны. SignalR автоматически обнаружит и инициализировать соответствующий транспортный протокол на основе функций, поддерживаемых на сервере и клиенте.

## <a name="hubs"></a>Концентраторы

SignalR использует концентраторы для обмена данными между клиентами и серверами.

Концентратор — общий конвейер, который позволяет клиентом и сервером, для вызова методов на друг с другом. SignalR обрабатывает доставку разных компьютерах автоматически, что позволяет клиентам вызывать методы на сервере, как легко, как локальные методы и наоборот. Концентраторы позволяют передачи строго типизированных параметров в методы, что позволяет привязки модели. SignalR обеспечивает два встроенных концентратор протокола: протокол текста на основе JSON и двоичный протокол, основанный на [MessagePack](https://msgpack.org/).  MessagePack обычно создает сообщения меньшего размера, чем при использовании JSON. Более старые браузеры должны поддерживать [XHR уровня 2](https://caniuse.com/#feat=xhr2) для обеспечения поддержки протокола MessagePack.

Концентраторы вызывать код на стороне клиента путем отправки сообщений с помощью активный транспорт. Сообщения содержат имя и параметры метода со стороны клиента. Объекты, которые передаются как параметры метода десериализуются с помощью настроенный протокол. Клиент пытается соответствовать имени метода в коде на стороне клиента. При появлении совпадения, выполнения метода клиента, используя десериализованные данные.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Начало работы с SignalR для ASP.NET Core](xref:tutorials/signalr)
* [Поддерживаемые платформы](xref:signalr/supported-platforms)
* [Центры](xref:signalr/hubs)
* [Клиент JavaScript](xref:signalr/javascript-client)
