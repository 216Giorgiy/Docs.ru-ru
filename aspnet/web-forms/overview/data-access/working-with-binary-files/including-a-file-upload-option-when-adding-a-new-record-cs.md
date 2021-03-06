---
uid: web-forms/overview/data-access/working-with-binary-files/including-a-file-upload-option-when-adding-a-new-record-cs
title: Параметр включения файла отправки при добавлении новой записи (C#) | Документация Майкрософт
author: rick-anderson
description: Этом руководстве показано, как создать веб-интерфейс, который позволяет пользователю ввести текстовых данных и передача двоичных файлов. Чтобы проиллюстрировать t доступные параметры...
ms.author: aspnetcontent
ms.date: 03/27/2007
ms.assetid: 362ade25-3965-4fb2-88d2-835c4786244f
msc.legacyurl: /web-forms/overview/data-access/working-with-binary-files/including-a-file-upload-option-when-adding-a-new-record-cs
msc.type: authoredcontent
ms.openlocfilehash: 5cc1db20a724c8a060e978e2360b977fb16f1e0c
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37842385"
---
<a name="including-a-file-upload-option-when-adding-a-new-record-c"></a>Включение параметра отправки файла, при добавлении новой записи (C#)
====================
по [Скотт Митчелл](https://twitter.com/ScottOnWriting)

[Скачайте пример приложения](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_56_CS.exe) или [скачать PDF](including-a-file-upload-option-when-adding-a-new-record-cs/_static/datatutorial56cs1.pdf)

> Этом руководстве показано, как создать веб-интерфейс, который позволяет пользователю ввести текстовых данных и передача двоичных файлов. Чтобы проиллюстрировать параметры, доступные для хранения двоичных данных, один файл будет сохраняться в базе данных, то время как другой хранятся в файловой системе.


## <a name="introduction"></a>Вступление

В предыдущих двух учебных курсах мы изучили методы для хранения двоичных данных, которая связана с моделью данных приложения s, коснулся того, как с помощью элемента управления FileUpload отправлять файлы из клиента на веб-сервер и увидели, как для представления двоичных данных в элементе данных W элемент управления ЭБ. Мы сохранить еще поговорим о том, как связать отправляемых данных с моделью данных, однако.

В этом руководстве мы создадим веб-страницы, чтобы добавить новую категорию. В дополнение к текстовые поля для категории s имя и описание этой странице потребуется включить два элемента управления FileUpload для нового изображения категории s и один для брошюры. Отправленного изображения сохраняются непосредственно в новой записи s `Picture` столбец, в то время как брошюры будет сохраняться в `~/Brochures` папку с путем к файлу, который сохранен в новой записи s `BrochurePath` столбца.

Перед созданием этой новой веб-странице, нам потребуется обновить архитектуры. `CategoriesTableAdapter` Основного запроса s не сможет извлечь `Picture` столбец. Следовательно, автоматически созданные `Insert` метод имеет только входные данные для `CategoryName`, `Description`, и `BrochurePath` поля. Таким образом, нам нужно создать метод в TableAdapter, которые запрашиваются все четыре `Categories` поля. `CategoriesBLL` Класса на уровне бизнес-логики также необходимо обновить.

## <a name="step-1-adding-aninsertwithpicturemethod-to-thecategoriestableadapter"></a>Шаг 1: Добавление`InsertWithPicture`метод`CategoriesTableAdapter`

Когда мы создали `CategoriesTableAdapter` обратно в [создание уровня доступа к данным](../introduction/creating-a-data-access-layer-cs.md) руководстве мы настроили его для автоматического создания `INSERT`, `UPDATE`, и `DELETE` инструкций на основе основного запроса. Кроме того, мы указали адаптер TableAdapter будет применять DB прямой подход, который создан методы `Insert`, `Update`, и `Delete`. Эти методы выполнения, автоматически созданные `INSERT`, `UPDATE`, и `DELETE` инструкций и, следовательно, принимать входные параметры, основанные на столбцах, возвращенной основным запросом. В [загрузка файлов](uploading-files-cs.md) учебник, дополнена необходимостью `CategoriesTableAdapter` s основного запроса для использования `BrochurePath` столбца.

Так как `CategoriesTableAdapter` s основного запроса не ссылается на `Picture` столбца, мы может добавить новую запись ни обновить существующую запись со значением для `Picture` столбца. Чтобы записать эти сведения, можно либо создать новый метод в TableAdapter, который используется специально для того, чтобы вставить запись с двоичными данными или мы можем настроить автоматически созданные `INSERT` инструкции. Проблемы в настройке, автоматически созданные `INSERT` инструкция является то, что мы возникает риск сделанных настроек, перезаписаны с помощью мастера. Например, представьте, что мы настроить `INSERT` инструкцию, чтобы включить использование `Picture` столбца. Это обновит TableAdapter s `Insert` метод, чтобы включить дополнительный входной параметр для двоичных данных изображения s категории s. Затем можно создать метод на уровне бизнес-логики, используйте этот метод DAL и вызова этого метода BLL через уровень представления данных, и все будет прекрасно работать. То есть до следующего настроена через мастер настройки адаптера таблицы TableAdapter. Сразу же после завершения работы мастера, сделанных настроек для `INSERT` инструкции будут перезаписаны, `Insert` метод будет возвращаться в форме со старой и больше не будет компилироваться, наш код!

> [!NOTE]
> Этот неудобством проблема не-при использовании хранимых процедур вместо специализированные инструкции SQL. Следующем учебном курсе рассматривается использование хранимых процедур вместо специализированные инструкции SQL на уровне доступа к данным.


Чтобы избежать этого потенциального головную боль, чем Настройка инструкций SQL автоматически созданный позволяют s вместо этого создайте новый метод для TableAdapter. Этот метод с именем `InsertWithPicture`, будет принимать значения для `CategoryName`, `Description`, `BrochurePath`, и `Picture` столбцов и выполнять `INSERT` инструкцию, которая хранит все четыре значения в новой записи.

Откройте типизированный набор DataSet и в режиме конструктора щелкните правой кнопкой мыши `CategoriesTableAdapter` s заголовка и выберите Добавить запрос в контекстном меню. Будет запущен мастер настройки запроса TableAdapter, который начинается с вопроса, как запроса адаптера таблицы должен получить доступ к базе данных. Выберите использовать инструкции SQL и нажмите кнопку Далее. Следующий шаг загрузчика для типа запроса. Так как мы повторно создав запрос, чтобы добавить новую запись для `Categories` таблицы, выберите INSERT и нажмите кнопку Далее.


[![Выберите параметр вставки](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image1.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image1.png)

**Рис. 1**: выберите параметр вставки ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image2.png))


Теперь нам нужно указать `INSERT` инструкции SQL. Мастер установки автоматически предлагает `INSERT` инструкции, соответствующий s основного запроса адаптера таблицы. В этом случае он s `INSERT` инструкции, которая вставляет `CategoryName`, `Description`, и `BrochurePath` значения. Инструкция UPDATE, чтобы `Picture` столбец входит в состав вдоль `@Picture` параметра, следующим образом:


[!code-sql[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample1.sql)]

На последнем экране мастера появляется запрос на имя нового метода адаптера таблицы. Введите `InsertWithPicture` и нажмите кнопку Готово.


[![Имя нового InsertWithPicture метод адаптера таблицы](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image2.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image3.png)

**Рис. 2**: назовите новый метод TableAdapter `InsertWithPicture` ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image4.png))


## <a name="step-2-updating-the-business-logic-layer"></a>Шаг 2: Обновление уровня бизнес-логики

Так как уровень представления только должны взаимодействовать с уровня бизнес-логики, а не на обход его, чтобы перейти непосредственно к уровня доступа к данным, необходимо создать метод BLL, который вызывает метод DAL, мы только что создали (`InsertWithPicture`). Для этого руководства, создайте метод в `CategoriesBLL` класс с именем `InsertWithPicture` , принимающий в качестве входных данных трех `string` s и `byte` массива. `string` Входные параметры являются имя категории s, описание и путь к файлу буклета, хотя `byte` массив предназначен для двоичного содержимого рисунка категории s. Как показано в следующем коде, этот метод BLL вызывает соответствующий метод DAL:


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample2.cs)]

> [!NOTE]
> Убедитесь, что вы сохранили типизированный набор DataSet перед добавлением `InsertWithPicture` метод BLL. Так как `CategoriesTableAdapter` код класса создается автоматически на основе на типизированный набор DataSet, если Дон t сначала сохранить изменения в типизированный набор DataSet `Adapter` свойство выиграл t знать о `InsertWithPicture` метод.


## <a name="step-3-listing-the-existing-categories-and-their-binary-data"></a>Шаг 3: Список существующих категорий и их двоичных данных

В этом руководстве мы создадим страницу, которая позволит пользователю добавить новую категорию в систему, предоставляя Брошюра и изображение для новой категории. В [предыдущем учебном курсе](displaying-binary-data-in-the-data-web-controls-cs.md) мы использовали элемент управления GridView с TemplateField и ImageField для отображения названия каждой категории s, описание, изображение и ссылка для загрузки его брошюры. Позвольте s реплицировать эту функциональность в данном случае создается страница, которая позволяет для новых создаваемых и перечислены все существующие категории.

Сначала откройте `DisplayOrDownload.aspx` странице из `BinaryData` папки. Перейдите в представление источника и скопируйте GridView и ObjectDataSource s декларативный синтаксис, вставив его в `<asp:Content>` элемент `UploadInDetailsView.aspx`. Кроме того, не забывайте следует заменять `GenerateBrochureLink` метода из класса кода программной части `DisplayOrDownload.aspx` для `UploadInDetailsView.aspx`.


[![Скопируйте и вставьте декларативный синтаксис из DisplayOrDownload.aspx UploadInDetailsView.aspx](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image3.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image5.png)

**Рис. 3**: скопируйте и вставьте декларативный синтаксис из `DisplayOrDownload.aspx` для `UploadInDetailsView.aspx` ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image6.png))


После копирования декларативный синтаксис и `GenerateBrochureLink` метод через `UploadInDetailsView.aspx` странице, откройте страницу в браузер, чтобы убедиться, что все, что была скопирована на правильно. Вы должны увидеть восемь категорий перечней GridView со ссылкой для загрузки брошюры, а также его категории s.


[![Теперь вы увидите каждой категории, а также его двоичных данных](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image4.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image7.png)

**Рис. 4**: вы должны теперь см. в разделе Each категории вместе с ее двоичными данными ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image8.png))


## <a name="step-4-configuring-thecategoriesdatasourceto-support-inserting"></a>Шаг 4: Настройка`CategoriesDataSource`вставке поддержки

`CategoriesDataSource` Используется элемент управления ObjectDataSource `Categories` GridView в настоящее время не предоставляет возможность вставки данных. Чтобы обеспечить поддержку вставки через этот элемент управления источником данных, необходимо сопоставить его `Insert` метода метод в свой базовый объект `CategoriesBLL`. В частности, необходимо сопоставить с `CategoriesBLL` мы добавили обратно на шаге 2, метод `InsertWithPicture`.

Запустить, щелкнув ссылку в смарт-теге ObjectDataSource s Настройка источника данных. Первый экран отображается источник данных настроен для работы с, объект `CategoriesBLL`. Оставьте этот параметр установлен равным- и нажмите кнопку Далее, чтобы перейти на экран задайте методы данных. Перейдите к вкладке «Вставка» и выбрать `InsertWithPicture` метода из раскрывающегося списка. Нажмите кнопку Готово, чтобы завершить работу мастера.


[![Настройка ObjectDataSource на использование метода InsertWithPicture](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image5.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image9.png)

**Рис. 5**: Настройка ObjectDataSource на использование `InsertWithPicture` метод ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image10.png))


> [!NOTE]
> После завершения работы мастера, Visual Studio может потребоваться, если вы хотите обновить поля и ключи, что приведет к повторному созданию данных Web управляет поля. Выберите "Нет", поскольку Выбор Да перезапишет изменения поля, которые могли быть внесены.


По завершении работы мастера ObjectDataSource теперь будет включать значение для его `InsertMethod` свойство производительны `InsertParameters` для столбцов четыре категории, как следующей декларативной разметке показано:


[!code-aspx[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample3.aspx)]

## <a name="step-5-creating-the-inserting-interface"></a>Шаг 5: Создание интерфейса вставки

Впервые охваченных [Обзор Вставка, обновление и удаление данных](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-cs.md), элемент управления DetailsView предоставляет встроенный интерфейс вставки, могут использоваться при работе с элементом управления источником данных, которая поддерживает вставку. Позвольте s добавьте элемент управления DetailsView на этой странице над элементом управления GridView, отображающего окончательно свой интерфейс для вставки, что позволяет пользователю быстро добавить новую категорию. После добавления новой категории в DetailsView, GridView под ним будет автоматически обновится и новой категории.

Начало путем перетаскивания с панели инструментов в конструктор над элементом управления GridView, установка DetailsView его `ID` свойства `NewCategory` и очищая `Height` и `Width` значения свойств. Смарт-теге DetailsView s, привязать его к существующему `CategoriesDataSource` и затем установите флажок Разрешить вставку.


[![Привязка элемента управления DetailsView CategoriesDataSource и включить вставку](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image6.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image11.png)

**Рис. 6**: привязка элемента управления DetailsView для `CategoriesDataSource` и разрешить вставку ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image12.png))


Чтобы окончательно отрисовки элемента управления DetailsView в свой интерфейс для вставки, задайте его `DefaultMode` свойства `Insert`.

Обратите внимание, что DetailsView пять полей BoundField `CategoryID`, `CategoryName`, `Description`, `NumberOfProducts`, и `BrochurePath` несмотря на то что `CategoryID` BoundField не отображается в интерфейсе правки, так как его `InsertVisible` свойство имеет значение `false`. Для этого существует, поскольку они являются столбцы, возвращаемые `GetCategories()` метод, который является элемент управления ObjectDataSource вызывает для извлечения данных. Для вставки, тем не менее, мы кое требуется t позволяет пользователю задать значение для `NumberOfProducts`. Кроме того мы должны разрешать их отправить изображение для новой категории, а также загрузить PDF-ФАЙЛ для брошюры.

Удалить `NumberOfProducts` BoundField DetailsView полностью и затем обновите `HeaderText` свойства `CategoryName` и `BrochurePath` поля BoundField, кроме категории и брошюры, соответственно. Затем преобразуйте `BrochurePath` BoundField в поле TemplateField и Добавление нового поля TemplateField для рисунка, предоставляя этого нового поля TemplateField `HeaderText` значение изображения. Переместить `Picture` TemplateField, чтобы оно было между `BrochurePath` TemplateField и CommandField.


![Привязка элемента управления DetailsView CategoriesDataSource и включить вставку](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image7.gif)

**Рис. 7**: привязка элемента управления DetailsView для `CategoriesDataSource` и включить вставку


Если же вы преобразовали `BrochurePath` включает в себя BoundField в поле TemplateField в диалоговом окне Изменить поля TemplateField `ItemTemplate`, `EditItemTemplate`, и `InsertItemTemplate`. Только `InsertItemTemplate` — требуется, однако приглашаю всех для удаления других двух шаблонах. На этом этапе декларативный синтаксис s DetailsView должен выглядеть следующим образом:


[!code-aspx[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample4.aspx)]

## <a name="adding-fileupload-controls-for-the-brochure-and-picture-fields"></a>Добавление элементов управления FileUpload брошюры и поля рисунков

В настоящее время `BrochurePath` TemplateField s `InsertItemTemplate` содержит текстовое поле, пока `Picture` TemplateField не содержит шаблонов. Необходимо обновить эти два s TemplateField `InsertItemTemplate` для использования элементов управления FileUpload.

Смарт-теге DetailsView s, выберите вариант изменить шаблоны, а затем выберите `BrochurePath` TemplateField s `InsertItemTemplate` из раскрывающегося списка. Удалить текстовое поле и перетащите элемент управления FileUpload из области элементов в шаблон. Значение элемента управления FileUpload s `ID` для `BrochureUpload`. Аналогичным образом, добавление элемента управления FileUpload для `Picture` TemplateField s `InsertItemTemplate`. Значение этого элемента управления FileUpload s `ID` для `PictureUpload`.


[![Добавление элемента управления FileUpload InsertItemTemplate](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image8.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image13.png)

**Рис. 8**: Добавление элемента управления FileUpload для `InsertItemTemplate` ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image14.png))


После внесения этих изменений, будет два TemplateField s декларативный синтаксис:


[!code-aspx[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample5.aspx)]

Когда пользователь добавляет новую категорию, мы стремимся обеспечить, что брошюры и изображение имеют тип правильный файл. Для брошюры пользователь должен указать PDF-ФАЙЛ. Изображения, вам понадобится пользователю загружать файл изображения, но мы, допускают *любой* образ файла или только файлы изображений определенного типа, таких как изображений GIF или JPGs? Чтобы разрешить для различных типов файлов, d необходимо расширить `Categories` схемы, чтобы включить столбец, который фиксирует тип файла, чтобы этот тип может быть отправлен клиенту через `Response.ContentType` в `DisplayCategoryPicture.aspx`. Поскольку мы кое t имеют такой столбец, было бы разумно ограничить пользователей только указание типа файла образа. `Categories` Изображения в существующей таблице s точечные рисунки, а JPGs — более подходящим форматом файлов изображений, обслуживаемых через Интернет.

Если пользователь отправляет неправильный тип, необходимо отменить вставки и выводит сообщение о проблеме. Добавьте элемент управления Label Web под DetailsView. Задайте его `ID` свойства `UploadWarning`снимите out его `Text` задайте `CssClass` свойств на "Предупреждение" и `Visible` и `EnableViewState` свойства `false`. `Warning` Класс CSS, определенные в `Styles.css` и отрисовывает текст в большой, красный, выделенный курсивом, полужирным шрифт.

> [!NOTE]
> В идеальном случае `CategoryName` и `Description` поля BoundField, кроме будет преобразована в поля TemplateField и их вставки интерфейсы, настроенные. `Description` Интерфейс, например, скорее всего лучше подходят в многострочном текстовом поле. А поскольку `CategoryName` столбец не принимает `NULL` значения, RequiredFieldValidator должен быть добавлен, чтобы гарантировать, пользователь предоставляет значение для нового имени категории s. Эти шаги остаются в качестве упражнения для чтения. Вернуться к [Настройка интерфейса изменения данных](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-cs.md) подробно рассмотрено дополнение интерфейсов изменения данных.


## <a name="step-6-saving-the-uploaded-brochure-to-the-web-server-s-file-system"></a>Шаг 6: Сохранение отправленного брошюры в файловой системе Web Server s

Когда пользователь вводит значения для новой категории и нажатии кнопки "Вставить", происходит обратная передача и операций по вставке окраску. Во-первых, DetailsView s [ `ItemInserting` событий](https://msdn.microsoft.com/library/system.web.ui.webcontrols.detailsview.iteminserting.aspx) активируется. Далее, ObjectDataSource s `Insert()` вызывается метод, что приводит к записи, добавляемые `Categories` таблицы. После этого DetailsView s [ `ItemInserted` событий](https://msdn.microsoft.com/library/system.web.ui.webcontrols.detailsview.iteminserted.aspx) активируется.

Прежде чем элемент управления ObjectDataSource s `Insert()` вызова метода, необходимо сначала убедитесь, что соответствующий файл типы были переданы пользователем и затем сохраните брошюры PDF-ФАЙЛ в файловой системе web server s. Создайте обработчик событий для DetailsView s `ItemInserting` событий и добавьте следующий код:


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample6.cs)]

Обработчик событий начинается со ссылки на `BrochureUpload` элемента управления FileUpload на основе шаблонов s DetailsView. Затем если передачи буклета, проверяется расширение s отправленного файла. Если расширение не содержится. PDF-ФАЙЛ, затем предупреждение отображается, вставки отменяется и завершения выполнения обработчика событий.

> [!NOTE]
> Полагаясь на расширение s отправленный файл не надежный способ убедитесь, что загруженный файл PDF-документ. Пользователь может иметь допустимый документ PDF-ФАЙЛ с расширением `.Brochure`, или может выполнены не PDF-документ и учитывая, что он `.pdf` расширения. Двоичное содержимое файла s необходимо программным образом проверить, чтобы более окончательно определить тип файла. Тщательное методов, однако зачастую избыточно; Проверка расширения достаточно для большинства сценариев.


Как уже говорилось в [загрузка файлов](uploading-files-cs.md) учебника, необходимо соблюдать осторожность при сохранении файлов в файловой системе, этой отправки одного пользователя s не перезаписывает другой s. В этом руководстве мы будет пытаться использовать то же имя как отправленный файл. Если уже существует файл в `~/Brochures` каталога с помощью этого имени файла, однако мы будем добавить номер в конце пока не будет найдено уникальное имя. Например, если пользователь передает буклет файл с именем `Meats.pdf`, но уже существует файл с именем `Meats.pdf` в `~/Brochures` папки, мы изменим имя сохраненного файла `Meats-1.pdf`. Если, существует, мы попытаемся `Meats-2.pdf`, и т. д., пока не будет найдено уникальное имя файла.

В следующем коде используется [ `File.Exists(path)` метод](https://msdn.microsoft.com/library/system.io.file.exists.aspx) для определения того, если файл уже существует с указанным именем файла. Если Да, он продолжает попробуйте новых имен файлов для брошюры, пока не будет найдено не было конфликтов.


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample7.cs)]

После обнаружения допустимое имя файла, файл необходимо сохранить в файловой системе и ObjectDataSource s `brochurePath``InsertParameter` значение должен быть обновлен, чтобы это имя файла записывается в базу данных. Как мы видели в *загрузка файлов* руководства, можно сохранить файл с помощью элемента управления FileUpload s `SaveAs(path)` метод. Необходимо обновить элемент управления ObjectDataSource `brochurePath` параметра, используйте `e.Values` коллекции.


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample8.cs)]

## <a name="step-7-saving-the-uploaded-picture-to-the-database"></a>Шаг 7: Сохранение отправленного изображения в базе данных

Для хранения отправленного изображения в новом `Categories` запись, нужно назначить отправленного двоичное содержимое в элемент управления ObjectDataSource s `picture` параметр в DetailsView s `ItemInserting` событий. До этого назначения, однако нам нужно сначала убедитесь, что отправленный изображен JPG-ФАЙЛ и не какое-либо иное образа. Как и в шаге 6 позволяют использовать расширение файла s отправленного изображения для получения его тип s.

Хотя `Categories` table допускает `NULL` значений в параметре `Picture` изображение имеет столбец, в настоящее время для всех категорий. Позвольте s заставить пользователя для получения картины, при добавлении новой категории этой странице. Следующий код проверяет, чтобы убедиться, что рисунок был загружен и имеет соответствующие расширения.


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample9.cs)]

Этот код должен быть помещен *перед* кода из шага 6 таким образом, если имеется проблема с передачей изображения, обработчик событий будет завершена, прежде чем буклет файл сохраняется в файловой системе.

Предположим, что соответствующий файл будет передан, назначьте отправленного двоичное содержимое s значение параметра рисунок со следующей строкой кода:


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample10.cs)]

## <a name="the-completeiteminsertingevent-handler"></a>Полный`ItemInserting`обработчик событий

Для полноты информации, вот `ItemInserting` обработчик событий в полном объеме:


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample11.cs)]

## <a name="step-8-fixing-thedisplaycategorypictureaspxpage"></a>Шаг 8: Устранение`DisplayCategoryPicture.aspx`страницы

Let s Отвлекитесь и проверьте его интерфейс вставки и `ItemInserting` обработчик событий, который был создан за последние несколько шагов. Посетите `UploadInDetailsView.aspx` странице через браузер и попытка добавить категорию, но не указывается рисунок, или указать рисунок не JPG или буклета не PDF. В любом из этих случаев отображается сообщение об ошибке и отменить рабочий процесс вставки.


[![Предупреждение будет отображаться, если передается недопустимый тип файла](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image9.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image15.png)

**Рис. 9**: предупреждающее сообщение будет отображаться, если передается недопустимый тип файла ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image16.png))


После проверки на странице необходим рисунка для отправки и реализованных t принимать файлы не PDF или не JPG, добавить новую категорию с допустимым изображением JPG, если оставить поле буклет пустым. После нажатия кнопки "Вставить", будет обратной передачи страницы и добавляется новая запись `Categories` таблицу с двоичное содержимое отправленного изображения s хранятся непосредственно в базе данных. Обновляется GridView и показана строка для вновь добавленного категории, но, как показано на рис. 10, новый рисунок s категории не отображается правильно.


[![Новая категория s рисунок не отображается.](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image10.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image17.png)

**Рис. 10**: s новой категории, изображение не отображается ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image18.png))


Новый рисунок не отображается, так как `DisplayCategoryPicture.aspx` страницу, которая возвращает фотографию указанной категории s настроена для обработки точечным рисункам, имеющим заголовок OLE. Этот заголовок 78 байтов отделен от `Picture` столбец s двоичное содержимое, перед их отправкой обратно клиенту. Но JPG-файл, мы отправили. для новой категории не поддерживает этот заголовок OLE; Таким образом допустимый, который требуется байт удаляются из s двоичные данные изображения.

Поскольку теперь оба точечные рисунки с заголовками OLE и JPGs в `Categories` таблицы, необходимо обновить `DisplayCategoryPicture.aspx` , чтобы он не заголовка OLE, чередует для исходного восьми категорий и обходит этот чередует для новых записей категории. В нашем следующем учебном курсе мы рассмотрим, как обновить существующий образ записи s, и мы объединим все старые изображения категории, чтобы они были JPGs. Сейчас, используйте следующий код в `DisplayCategoryPicture.aspx` для удаления заголовков OLE только для этих исходного восьми категорий:


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample12.cs)]

Благодаря этому изменению изображение JPG теперь отображается правильно в GridView.


[![JPG для новых категорий являются правильно к просмотру](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image11.gif)](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image19.png)

**Рис. 11**: JPG образов для новых категорий, подготовке к просмотру правильно ([Просмотр полноразмерного изображения](including-a-file-upload-option-when-adding-a-new-record-cs/_static/image20.png))


## <a name="step-9-deleting-the-brochure-in-the-face-of-an-exception"></a>Шаг 9: Удаление брошюры Столкнувшись с исключением

Одной из проблем хранения двоичных данных в файловой системе s web server является то, что появляется к разрыву соединения модели данных и его двоичные данные. Таким образом при удалении записи соответствующих двоичных данных в файловой системе необходимо также удалить. Это можно учитывать при вставке, а также. Рассмотрим следующий сценарий: пользователь добавляет новую категорию, указав допустимое изображение и брошюры. Нажав кнопку "Добавить", происходит обратная передача и DetailsView s `ItemInserting` вызывает событие, сохранение брошюры в файловой системе сервера s web. Далее, ObjectDataSource s `Insert()` вызывается метод, который вызывает `CategoriesBLL` класс s `InsertWithPicture` метод, который вызывает `CategoriesTableAdapter` s `InsertWithPicture` метод.

Теперь, что произойдет, если база данных находится в автономном режиме или если возникает ошибка в `INSERT` инструкции SQL? Четко вставки не удастся, поэтому нет новой категории строки будут добавляться в базу данных. Но мы по-прежнему отправленного буклет файл, находящийся в файловой системе веб сервера s! Этот файл должен быть удален при возникновении исключения во время операций по вставке.

Как было описано ранее в [обработка BLL и исключения уровня DAL на странице ASP.NET](../editing-inserting-and-deleting-data/handling-bll-and-dal-level-exceptions-in-an-asp-net-page-cs.md) руководстве описано, когда исключение вызывается в глубине архитектуры, оно "поднимается" через различные уровни. На уровне представления, мы можем определить, если возникло исключение из DetailsView s `ItemInserted` событий. Этот обработчик событий также предоставляет значения ObjectDataSource s `InsertParameters`. Таким образом, можно создать обработчик событий для `ItemInserted` событие, которое проверяет, если возникло исключение и если да, удаляет файл, указанный параметром ObjectDataSource s `brochurePath` параметр:


[!code-csharp[Main](including-a-file-upload-option-when-adding-a-new-record-cs/samples/sample13.cs)]

## <a name="summary"></a>Сводка

Существует ряд действий, которые должны быть выполнены для предоставления веб-интерфейс для добавления записи, которые содержат двоичные данные. Если двоичные данные хранятся непосредственно в базу данных, скорее всего, вам потребуется обновить архитектуры, добавление определенных методов для обработки случая, вставки двоичных данных. После обновления архитектуры, следующим шагом будет создание интерфейс вставки, который может быть выполнено с помощью элемент DetailsView, был настроен для включения элемента управления FileUpload для каждого поля двоичных данных. Загруженные данные затем можно сохранить в файловой системе сервера s web или назначено для параметра источника данных в DetailsView s `ItemInserting` обработчик событий.

Сохранение двоичных данных в файловой системе требует более планирования, чем сохранение данных непосредственно в базу данных. Чтобы избежать передачи одного пользователя s, перезапись другой s необходимо выбрать схему именования. Кроме того дополнительные шаги необходимо выполнить удалить загруженный файл, если вставка базы данных завершается ошибкой.

Теперь у нас есть возможность добавлять новые категории в системе с буклета, изображения, но мы ve еще перейти на обновление существующих категории s двоичных данных или как правильно удалить двоичные данные для удаленных категории. В следующем учебном курсе мы рассмотрим следующие две темы.

Счастливого вам программирования!

## <a name="about-the-author"></a>Об авторе

[Скотт Митчелл](http://www.4guysfromrolla.com/ScottMitchell.shtml), автор семи книг по ASP/ASP.NET и основатель веб- [4GuysFromRolla.com](http://www.4guysfromrolla.com), работает с веб-технологиями Microsoft с 1998 года. Скотт — независимый консультант, преподаватель и автор. Его последняя книга — [ *Sams Teach ASP.NET 2.0 in 24 часа*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Ним можно связаться по адресу [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) или через его блог, который можно найти в [ http://ScottOnWriting.NET ](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Особая благодарность

В этой серии руководств пособий рецензировалась многими компетентными редакторами. Дейв Gardner Терезой Мерфи и Екатерина Leigh, стали Лиз Шалок в этом руководстве. Хотите поработать с моих последующих статей для MSDN? Если Да, напишите мне [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

> [!div class="step-by-step"]
> [Назад](displaying-binary-data-in-the-data-web-controls-cs.md)
> [Вперед](updating-and-deleting-existing-binary-data-cs.md)
