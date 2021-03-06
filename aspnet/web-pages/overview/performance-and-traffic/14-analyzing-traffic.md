---
uid: web-pages/overview/performance-and-traffic/14-analyzing-traffic
title: Отслеживание сведений о посетителях (Analytics) для веб-страниц ASP.NET (Razor) сайта | Документация Майкрософт
author: tfitzmac
description: После вы азы освоите веб-сайт будет, можно анализировать трафик веб-сайта.
ms.author: aspnetcontent
ms.date: 02/17/2014
ms.assetid: 360bc6e1-84c5-4b8e-a84c-ea48ab807aa4
msc.legacyurl: /web-pages/overview/performance-and-traffic/14-analyzing-traffic
msc.type: authoredcontent
ms.openlocfilehash: 4e065e5223d2f996779ab47de4823962a9aa852e
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37831100"
---
<a name="tracking-visitor-information-analytics-for-an-aspnet-web-pages-razor-site"></a>Отслеживание сведений о посетителях (Analytics) для узла ASP.NET Web Pages (Razor)
====================
по [Tom FitzMacken](https://github.com/tfitzmac)

> В этой статье описывается, как использовать вспомогательный объект для добавления аналитики веб-сайтов на страницы на веб-сайте ASP.NET Web Pages (Razor).
> 
> Вы узнаете, как:
> 
> - Как отправлять сведения о трафике вашего веб-сайта в поставщик данных аналитики.
> 
> Ниже перечислены возможности, представленные в этой статье программирования ASP.NET:
> 
> - `Analytics` Вспомогательный.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Версии программного обеспечения, используемые в этом руководстве
> 
> 
> - Веб-страниц ASP.NET (Razor) 2
> - Библиотеку ASP.NET (пакет NuGet)


Аналитика — это общий термин для технология, которая измеряет трафик на веб-сайте, чтобы вы могли оценить, как пользователи работают с сайта. Доступны многие службы аналитики, включая службы Google, Yahoo, StatCounter и другие.

Аналитики так работает, что вам зарегистрироваться для учетной записи с помощью поставщика аналитики, где выполняется регистрация узла, которые требуется отслеживать. Поставщик отправляет фрагмент кода JavaScript, который включает в себя идентификатор или код для вашей учетной записи трассировки. Добавьте фрагмент JavaScript на веб-страницы на сайте, который вы хотите отслеживать. (Обычно добавляется фрагмент аналитики на страницу макета или верхний колонтитул или другие HTML-разметка, отображался на каждой странице на сайте.) При запросе страницы, которая содержит один из этих фрагментов кода JavaScript, фрагмент кода отправляет сведения о текущей странице поставщика аналитики, который записывает различные сведения о странице.

Если вы хотите взглянуть на статистики сайта, вы Войдите на веб-сайт поставщика аналитики. Затем можно просмотреть все виды отчетов о веб-узле, например:

- Число просмотров страниц для отдельных страниц. Это означает, (приблизительно) Узнайте, сколько пользователей посещают сайт, и страницы на сайте используются наиболее часто.
- Сколько пользователей можно потратить на определенные страницы. Это может сообщить, например мешает ли домашнюю страницу процент пользователей.
- Тем, кто сайтов находились, прежде чем они посетит веб-узла. Это поможет вам понять, поступил ли ваш трафик по ссылкам из поисковые запросы и т. д.
- При посещении веб-узла, и как долго они остаются.
- Каких стран, к которой принадлежат посетителей.
- Какие браузеры и операционные системы при использовании посетителей.

    ![Ch14traffic-1](14-analyzing-traffic/_static/image1.jpg)

## <a name="using-a-helper-to-add-analytics-to-a-page"></a>Использование вспомогательного метода для добавления аналитики на страницу

Веб-страниц ASP.NET включает в себя несколько вспомогательных функций аналитики (`Analytics.GetGoogleHtml`, `Analytics.GetYahooHtml`, и `Analytics.GetStatCounterHtml`), облегчающие управление фрагменты кода JavaScript, используемых для аналитики. Вместо придумать, как и где помещу код JavaScript, что необходимо сделать всего лишь добавить вспомогательную функцию для страницы. Единственные сведения, необходимые для предоставления является ваши имя учетной записи, идентификатор или код трассировки. (Для StatCounter, также необходимо предоставить несколько дополнительных значений.)

В этой процедуре вы создадите макет страницы, использующей `GetGoogleHtml` вспомогательный. Если уже есть учетная запись с одним из других поставщиков аналитики, можно вместо этого использовать эту учетную запись и необходимости вносить небольшие изменения.

> [!NOTE]
> При создании учетной записи аналитики, вы регистрируете URL-адрес сайта, который требуется отслеживать. Если вы тестируете все, что на локальном компьютере, не отслеживания фактического трафика (трафик только тех, которые), поэтому вы не сможете регистрировать и просматривать статистику сайта. Но эта процедура показывает, как добавить вспомогательный аналитики на страницу. При публикации узла активного сайта будет отправлять сведения поставщику аналитики.


1. Добавьте библиотеку ASP.NET на веб-сайт, как описано в [Установка вспомогательных функций на сайте веб-страниц ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), если не было добавлено.
2. Создание учетной записи с помощью Google Analytics и запишите имя учетной записи.
3. Создание макета страницы с именем *Analytics.cshtml* и добавьте следующую разметку:

    [!code-cshtml[Main](14-analyzing-traffic/samples/sample1.cshtml)]

    > [!NOTE]
    > Необходимо поместить вызов `Analytics` вспомогательная функция в теле веб-страницы (до `</body>` тега). В противном случае — обозреватель не запускает сценарий.

    Если вы используете поставщик разных analytics, используйте один из следующих вспомогательных методов:

    - (Yahoo) `@Analytics.GetYahooHtml("myaccount")`
    - (StatCounter) `@Analytics.GetStatCounterHtml("project", "security")`
4. Замените `myaccount` с именем учетной записи, идентификатор или код трассировки, созданный на шаге 1.
5. Откройте страницу в браузере. (Убедитесь, что выбран страницы **файлы** рабочей области, прежде чем запускать его.)
6. В браузере просмотрите исходный код страницы. Вы сможете см. в разделе аналитики, готовый для просмотра кода:

    [!code-html[Main](14-analyzing-traffic/samples/sample2.html)]
7. Войдите на сайт Google Analytics и статистику для вашего сайта. Если вы используете страницы на действующем сайте, вы увидите запись, которая регистрирует посетите страницу.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Дополнительные ресурсы

- [Сайт Google Analytics](https://www.google.com/analytics/)
- [Yahoo! Web Analytics сайта](http://help.yahoo.com/l/us/yahoo/ywa/)
- [StatCounter сайта](http://statcounter.com/)
