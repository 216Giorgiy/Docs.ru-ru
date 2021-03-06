---
uid: web-forms/overview/ajax-control-toolkit/animation/picking-one-animation-out-of-a-list-cs
title: Выбор одной анимации из списка (C#) | Документация Майкрософт
author: wenz
description: Отображается этот элемент управления в ASP.NET AJAX Control Toolkit не только элемент управления, но всю платформу для добавления анимации в элемент управления. Платформа также ра...
ms.author: aspnetcontent
ms.date: 06/02/2008
ms.assetid: 06a776fe-7c73-4ca7-8e02-5260a86edc03
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/picking-one-animation-out-of-a-list-cs
msc.type: authoredcontent
ms.openlocfilehash: 7ef2c5d37c32150d17b798e22290f33b5619a14c
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37834676"
---
<a name="picking-one-animation-out-of-a-list-c"></a>Выбор одной анимации из списка (C#)
====================
по [Кристиан Wenz](https://github.com/wenz)

[Скачать код](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation5.cs.zip) или [скачать PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation5CS.pdf)

> Отображается этот элемент управления в ASP.NET AJAX Control Toolkit не только элемент управления, но всю платформу для добавления анимации в элемент управления. Платформа также позволяет программисту Выбор одной анимации из списка анимации, в зависимости от оценки кода JavaScript.


## <a name="overview"></a>Обзор

Отображается этот элемент управления в ASP.NET AJAX Control Toolkit не только элемент управления, но всю платформу для добавления анимации в элемент управления. Платформа также позволяет программисту Выбор одной анимации из списка анимации, в зависимости от оценки кода JavaScript.

## <a name="steps"></a>Шаги

Во-первых, включите `ScriptManager` страницы; затем ASP.NET AJAX library загружена, что позволяет использовать набор средств управления:

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample1.aspx)]

Анимация будет применяться к панели текста, который выглядит следующим образом:

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample2.aspx)]

В связанный класс CSS для панели определить цвет фона, удобная и также установить фиксированную ширину для панели:

[!code-css[Main](picking-one-animation-out-of-a-list-cs/samples/sample3.css)]

Затем добавьте `AnimationExtender` на страницу, предоставляя `ID`, `TargetControlID` атрибут и обязательным `runat="server":`

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample4.aspx)]

В рамках `<Animations>` узла, используйте `<OnLoad>` для запуска анимации после полной загрузки страницы. Вместо одного из обычных анимаций `<Case>` элемент вступает в действие. Значение атрибута SelectScript вычисляется; Возвращаемое значение должно быть числовым. В зависимости от того, это число, один из subanimations в &lt;случай&gt; выполняется. Например, если SelectScript равно 2, Control Toolkit выполняется третий анимации в &lt;случай&gt; (перечисление начинается с 0).

Следующая разметка определяет три subanimations: изменение размера ширины и изменение размера Высота исчезновение. Код JavaScript (`Math.floor(3 * Math.random())`) затем выбирает число от 0 до 2, так, что выполняется одно из трех анимации:

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample5.aspx)]


[![Один из возможных три анимации: Получает ширину панели](picking-one-animation-out-of-a-list-cs/_static/image2.png)](picking-one-animation-out-of-a-list-cs/_static/image1.png)

Один из возможных три анимации: Получает ширину панели ([Просмотр полноразмерного изображения](picking-one-animation-out-of-a-list-cs/_static/image3.png))

> [!div class="step-by-step"]
> [Назад](animation-depending-on-a-condition-cs.md)
> [Вперед](animating-in-response-to-user-interaction-cs.md)
