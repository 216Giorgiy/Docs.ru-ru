---
uid: mvc/overview/getting-started/introduction/adding-a-controller
title: Добавление контроллера | Документация Майкрософт
author: Rick-Anderson
description: ''
ms.author: aspnetcontent
ms.date: 10/17/2013
ms.assetid: cc764f3b-6921-486a-8f44-c6ccd1249acd
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-controller
msc.type: authoredcontent
ms.openlocfilehash: 77389bfa4774857eb2a607b0a70e982826312e03
ms.sourcegitcommit: b8a2f14bf8dd346d7592977642b610bbcb0b0757
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38119267"
---
<a name="adding-a-controller"></a>Добавление контроллера
====================
по [Рик Андерсон](https://github.com/Rick-Anderson)

[!INCLUDE [Tutorial Note](sample/code-location.md)]

MVC означает *model-view-controller*. MVC — это шаблон для разработки приложений, которые хорошо продуманной архитектурой, пригодного для тестирования и простые в обслуживании. Приложения на основе MVC содержат:

- **M** odels: классы, представляющие данные приложения и используют логику проверки для реализации бизнес-правил к этим данным.
- **V** редставления: файлы шаблонов, которые приложение использует для динамического создания HTML-ответов.
- **C** ontrollers: классы, которые обрабатывают входящие запросы браузера, извлекают данные модели, а затем укажите шаблоны представлений, которые возвращают ответ в браузере.

Мы будет обсуждаться в этой серии руководств, все эти концепции и показать, как их использовать для создания приложения.

> [!NOTE]
> Шаблон был выбран на предыдущем шаге MVC по умолчанию. При этом создается главная, учетная запись и управление контроллерами, по умолчанию.

Начнем с создания класса контроллера. В **обозревателе решений**, щелкните правой кнопкой мыши *контроллеров* папку и нажмите кнопку **добавить**, затем **контроллера**.


![](adding-a-controller/_static/image1.png)

В **Добавление шаблона** диалоговом окне щелкните **контроллер MVC 5 — пустой**, а затем нажмите кнопку **добавить**.

![](adding-a-controller/_static/image2.png)  
 

Назовите новый контроллер «HelloWorldController» и нажмите кнопку **добавить**.

![Добавление контроллера](adding-a-controller/_static/image3.png)

Обратите внимание, что в **обозревателе решений** что новый файл был создан именованный *HelloWorldController.cs* и ее *Views\HelloWorld*. Контроллер открыт в интегрированной среде разработки.

![](adding-a-controller/_static/image4.png)

Замените содержимое файла следующим кодом.

[!code-csharp[Main](adding-a-controller/samples/sample1.cs)]

Методы контроллера будет возвращена строка HTML, в качестве примера. Контроллер называется `HelloWorldController` и первый метод называется `Index`. Давайте вызвать его из браузера. Запустите приложение (нажмите клавишу F5 или Ctrl + F5). В браузере, добавьте &quot;HelloWorld&quot; к пути в адресной строке. (Например, на рисунке ниже, его `http://localhost:1234/HelloWorld.`) страница в браузере будет выглядеть как на следующем снимке экрана. В методе выше код вернул строку непосредственно. Вы сказали, что система должна возвращать только некоторые HTML, как и!

![](adding-a-controller/_static/image5.png)

ASP.NET MVC вызывает классы другой контроллер (и различных методов действий в них), в зависимости от входящего URL-адреса. Логику маршрутизации URL-адрес по умолчанию используется ASP.NET MVC использует формат следующим образом, чтобы определить, какой код для вызова:

`/[Controller]/[ActionName]/[Parameters]`

Формат маршрутизации задается *приложения\_Start/RouteConfig.cs* файла.

[!code-csharp[Main](adding-a-controller/samples/sample2.cs?highlight=7-8)]

При запуске приложения и не указывайте все сегменты URL-адреса, по умолчанию используется «Home» контроллера и действия метод «Index», заданный в раздел defaults приведенный выше код.

Первая часть URL-адреса определяет класс контроллера для выполнения. Поэтому */HelloWorld* сопоставляется `HelloWorldController` класса. Вторая часть URL-адреса определяет метод действия к классу для выполнения. Поэтому */HelloWorld/индекс* вызовет `Index` метод `HelloWorldController` класс для выполнения. Обратите внимание, что нам пришлось перейдите к */HelloWorld* и `Index` метод использовался по умолчанию. Это обусловлено тем, метод с именем `Index` является методом по умолчанию, который будет вызываться на контроллере, если тип не задан явным образом. В третьей части сегмента URL-адреса (`Parameters`) указываются данные маршрута. Данные маршрута будет показано позже в этом руководстве.

Перейдите по адресу `http://localhost:xxxx/HelloWorld/Welcome`. `Welcome` Метод запускается и возвращает строку &quot;метод приветствия действие... &quot;. MVC по умолчанию осуществляется сопоставление `/[Controller]/[ActionName]/[Parameters]`. Для этого URL-адреса заданы контроллер `HelloWorld` и метод действия `Welcome`. Часть URL-адреса `[Parameters]` на данный момент еще не использовалась.

![](adding-a-controller/_static/image6.png)

Давайте немного изменить приведенный пример, чтобы передать сведения о параметрах из URL-адрес в контроллер (например, */HelloWorld/Welcome? имя = Скотт&amp;; numtimes = = 4*). Изменение вашего `Welcome` метод включив два параметра, как показано ниже. Обратите внимание, что код использует функцию необязательного параметра C#, чтобы указать, что `numTimes` параметр должен по умолчанию 1, если не передаются значения для этого параметра.

[!code-csharp[Main](adding-a-controller/samples/sample3.cs)]

> [!NOTE]
> Примечание по безопасности: Приведенном выше примере используется [HttpUtility.HtmlEncode](https://msdn.microsoft.com/library/ee360286(v=vs.110).aspx) для защиты приложения от злонамеренного ввода данных (JavaScript). Дополнительные сведения см. в разделе [как: защита от скриптов в веб-приложении внедрения в строки](https://msdn.microsoft.com/library/a2a4yykt(v=vs.100).aspx).


 Запустите приложение и перейдите к пример URL-адреса (`http://localhost:xxxx/HelloWorld/Welcome?name=Scott&numtimes=4`). Вы можете попробовать различные значения для `name` и `numtimes` в URL-адрес. [Система привязки модели ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) автоматически сопоставляет именованные параметры из строки запроса в адресной строке параметров метода.

![](adding-a-controller/_static/image7.png)

В примере выше, сегмент URL-адреса ( `Parameters`) не используется, `name` и `numTimes` параметры передаются как [строки запроса](http://en.wikipedia.org/wiki/Query_string). Подстановочный знак ? (вопросительный знак) в приведенном выше URL-адрес является разделителем и указываются строки запроса. Символ &amp; разделяет строки запроса.

Замените метод приветствия следующим кодом:

[!code-csharp[Main](adding-a-controller/samples/sample4.cs)]

Запустите приложение и введите следующий URL-адрес: `http://localhost:xxx/HelloWorld/Welcome/1?name=Scott`

![](adding-a-controller/_static/image8.png)

Этот раз третий сегмент URL-адрес сопоставляется с параметром маршрута `ID.` `Welcome` метод действия содержит параметр (`ID`), соответствующих спецификации URL-адрес в `RegisterRoutes` метод.

[!code-csharp[Main](adding-a-controller/samples/sample5.cs?highlight=7)]

В приложениях ASP.NET MVC обычно передавать параметры в качестве данных маршрута (как это делалось с Идентификатором выше), чем передавать их в качестве строки запроса. Можно также добавить маршрут для передачи оба `name` и `numtimes` в параметры в качестве данных маршрута в URL-адрес. В *приложения\_Start\RouteConfig.cs* добавьте маршрут «Hello»:

[!code-csharp[Main](adding-a-controller/samples/sample6.cs?highlight=13-16)]

Запустите приложение и перейдите к `/localhost:XXX/HelloWorld/Welcome/Scott/3`.

![](adding-a-controller/_static/image9.png)

Для многих приложений MVC маршрут по умолчанию работает нормально. Вы узнаете далее в этом руководстве для передачи данных с помощью связывателя модели, и вам не придется изменить маршрут по умолчанию для этого.

В этих примерах контроллер предоставляет &quot;VC&quot; модели MVC — то есть работу представления и контроллера. Контроллер возвращает HTML напрямую. Обычно это не требуется Возвращает HTML напрямую, поскольку в этом случае заметно усложняется написание кода. Вместо этого мы обычно используем файл шаблона отдельное представление для создания HTML-ответа. Давайте взглянем далее в том, как это можно сделать.

> [!div class="step-by-step"]
> [Назад](getting-started.md)
> [Вперед](adding-a-view.md)
