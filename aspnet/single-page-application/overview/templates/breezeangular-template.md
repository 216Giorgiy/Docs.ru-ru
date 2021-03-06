---
uid: single-page-application/overview/templates/breezeangular-template
title: Шаблон breeze/Angular | Документация Майкрософт
author: madskristensen
description: Шаблон breeze/Angular одностраничного приложения
ms.author: aspnetcontent
ms.date: 03/08/2013
ms.assetid: db31e909-563a-4516-aadd-62aa210ac7e4
msc.legacyurl: /single-page-application/overview/templates/breezeangular-template
msc.type: authoredcontent
ms.openlocfilehash: 3799c891625b28312b54ed33628748dcc1b74925
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37811754"
---
<a name="breezeangular-template"></a>Шаблон breeze/Angular
====================
по [Мэдсом Kristensen](https://github.com/madskristensen)

> Шаблон Breeze/Angular было написано с Ворд колокольчика
> 
> [Загрузить шаблон Breeze/Angular](https://go.microsoft.com/fwlink/?LinkId=286437)


[AngularJS](http://angularjs.org) является библиотекой с открытым исходным кодом от Google для построения одностраничных приложений (SPA). Он предлагает привязки данных, введение зависимостей и управления на экране. Объединить ее с [BreezeJS](http://www.breezejs.com/?utm_source=ms-spa), главные этапы превосходных приложений клиента HTML/JavaScript имеют другой библиотека с открытым кодом для моделирования данных и управления данными.

Шаблон Breeze/Angular SPA является вариантом на [шаблон KnockoutJS SPA](../introduction/knockoutjs-template.md) включенным в ASP.NET и веб-инструменты 2012.2 обновление. Если у вас есть Visual Studio, вы получите пример одностраничного приложения приступить к работе в менее чем за 60 секунд.

![](http://www.breezejs.com/sites/all/images/spa-template/NgRunningTodoPage.png)

Внешне приложение выглядит очень похоже на шаблон KnockoutJS SPA. Но это совсем другой взгляд изнутри. Шаблон KnockoutJS использует Knockout для привязки данных и необработанные AJAX для доступа к данным. Шаблон Breeze/Angular использует Angular для привязки данных и Breeze для доступа к данным. Эти библиотеки использовать дополнительные возможности, включая навигацию по страницам и журнал.

Вот страницу About приложения:

![](http://www.breezejs.com/sites/all/images/spa-template/NgRunningAboutPage.png)

На этой странице отображаются регистрируемые события в текущем сеансе пользователя, включая:

- Разбиение по страницам. Обратите внимание, создание контроллера Todo с #2 и #7.
- Удаленные запросы (3) и локальный кэш запросов (7).
- Сохранение новой (5, #6) и изменения сущностей (4).
- Изменения, проверки на стороне клиента (9), поэтому пользователь может исправления ошибок, перед внесением изменений, к базе данных.

Больше, изучите в этом шаблоне, включая:

- Динамическая загрузка шаблонов представление HTML.
- Привязка пользовательских данных через Angular «директивы».
- Внедрение модульности и зависимостей.
- Фильтры запросов, сортировка, разбиение по страницам, проекции и включения связанных сущностей.
- Совместное использование данных на нескольких экранах.
- Идет сохранение нескольких изменений как одна транзакция.
- Правила проверки автоматически распространяются на сервере для клиента JavaScript.

Итак, начнем.

## <a name="create-a-breezeangular-template-project"></a>Создайте проект шаблон Breeze/Angular

Скачайте и установите шаблон, нажав кнопку "скачать" выше. Шаблон упаковывается в формате Visual Studio Extension (VSIX). Может потребоваться перезапустить Visual Studio.

В **шаблоны** области выберите **установленные шаблоны** и разверните **Visual C#** узла. В разделе **Visual C#** выберите **Web**. В списке шаблонов проектов выберите **веб-приложение ASP.NET MVC 4**. Имя проекта и нажмите кнопку **ОК**.

В **новый проект** мастера выберите **Breeze Angular SPA**.

![](http://www.breezejs.com/sites/all/images/spa-template/SelectBreezeNgSpaTemplate.png)

Нажмите клавиши Ctrl-F5, чтобы построить и запустить приложение без отладки, или нажмите клавишу F5, чтобы запустить программу с отладкой.

![](http://www.breezejs.com/sites/all/images/spa-template/ZephyrLogin.png)

При первом запуске приложения отображается экран входа. Щелкните ссылку «Регистрация» и glides новую страницу в представлении, где можно ввести имя пользователя и пароль. (На страницах входа и регистрации создаются с использованием ASP.NET MVC.) Когда вы отправляете форму регистрации, сервер создает TodoList с двумя элементами для вашей учетной записи. Затем отображаемыми их на желтый ноте.

![](http://www.breezejs.com/sites/all/images/spa-template/TodoList.png)

Теперь вы находитесь в land одностраничного приложения. Все, что вы просматриваете и возникают во время обработки задач к просмотру и управляется на клиенте с помощью Knockout и Breeze. Обзор приложения от имени пользователя... но с изображением глаза для разработчика. Использование средств разработчика в браузере для записи сетевого трафика. (В Internet Explorer: нажмите клавишу F12, выберите **сети** , а щелкните **начать захват**.) Теперь попробуйте сделайте следующее:

- Добавьте новый элемент списка дел.
- Щелкните метку и изменить заголовок элемента списка дел
- Установите флажок, чтобы пометить элемент done. Обратите внимание, что текстовое поле отключено, поэтому заголовок больше не является редактируемой.
- Щелкните значок «x» справа от метки. Элемент исчезнет и удаляется из базы данных.
- Выберите другой элемент и очистить его заголовок. Вы получите ошибку проверки, что заголовок является обязательным. После небольшой паузы восстанавливается предыдущих заголовок.
- Введите в очень длинный заголовок. Вы получите ошибку проверки, название слишком длинное.
- Нажмите кнопку «Добавить Todo List». Появится новый список слева от приведенного выше списка.
- Воспроизведение с заголовком TodoList, активируя ее и проверки длины.
- Щелкните текстовое поле заголовка, чтобы очистить сообщение об ошибке.
- Нажмите кнопку «x» в круге в правом верхнем углу, чтобы удалить TodoList и его задач.
- Щелкните ссылку «About» в правом верхнем углу, чтобы просмотреть журнал выполнения этих действий.

Логика проверки реализуется выполнено стороне клиента с очень просто. Атрибуты проверки в классах модели сервера передаются клиенту и выполняется автоматически, прежде чем клиент связывается с сервером.

Просмотрите сетевой трафик. Обратите внимание, что факт никакие вызовы на сервер при очень просто обнаружена ошибка. Каждое допустимое Изменение привело к запрос POST к «/ api/Todo/SaveChanges». Очень просто объединяет изменения и отправляет их друг с другом виде одного запроса контроллер Web API `SaveChanges` метод. Это отличается от шаблона KockoutJS SPA, что делает PUT, POST и DELETE запросов для каждого элемента по отдельности.

Кроме того Обратите внимание, что сетевой трафик при переключении между TodoList, а также о страницах. Том, что запрос является ограниченной в локальный кэш очень просто.

## <a name="peek-inside"></a>Показать внутри

Это приложение имеет стороне клиента и на стороне сервера. Стек клиентского состоит из немного HTML и сочетание модули приложения JavaScript (в папке «приложение») плюс сторонних библиотек JavaScript (в папке «Сценарии»).

![](http://www.breezejs.com/sites/all/images/spa-template/NgClientArchitecture2.png)

Архитектура пользовательского интерфейса разделяет мини-приложения HTML из представления в код презентации в контроллерах. Angular система привязки данных координирует представлениях и контроллерах, что каждый может выполнять свою работу без глубокого знания другого.

Контроллер запрашивает контекст данных для получения и сохранения модели сущностей. Контекст данных делегирует большая часть работы по Breeze, которая создает модель объектов с самостоятельным отслеживанием из результатов запроса JSON.

На сервере стек состоит из трех библиотек .NET принцип и код разработчика: веб-API, платформа Entity Framework и Breeze.NET:

![](http://www.breezejs.com/sites/all/images/spa-template/ServerArchitecture.png)

Базовая архитектура совпадает со значением шаблона KockoutJS SPA. Тем не менее реализация гораздо проще: DTO были удалены, и большая часть деталей Entity Framework делегированных Breeze.NET.

## <a name="next-steps"></a>Следующие шаги

Мы рекомендуем просмотреть код, занятия под руководством [обширной обсуждение](http://www.breezejs.com/ng-spa-template?utm_source=ms-spa) клиента и стеки серверов, на веб-сайте очень просто.

Можно попробовать воспроизведение с помощью запросов на стороне клиента очень просто; Добавьте некоторые фильтры и выполнять сортировку. Можно добавить дополнительные свойства модели и дополнительные сущности, чтобы лучше понять, для разработки SPA end-to-end. Если есть уверенность, разработки, можно разделять работу функций Todo, а затем заменить их на собственные.

Удачного программирования!
