---
title: Начало работы с ASP.NET Core
author: rick-anderson
description: Краткий учебник, в котором с помощью ASP.NET Core создается и запускается простое приложение Hello World.
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2018
uid: getting-started
ms.openlocfilehash: 7ab9f303d74786c4ac76f002d0f2c66371e78cb8
ms.sourcegitcommit: b4c7b1a4c48dec0865f27874275c73da1f75e918
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228586"
---
# <a name="get-started-with-aspnet-core"></a>Начало работы с ASP.NET Core

::: moniker range=">= aspnetcore-2.1"

1. Установите [!INCLUDE [](~/includes/2.1-SDK.md)].

2. Создайте проект ASP.NET Core. Откройте окно командной оболочки и введите следующую команду.

    ```console
    dotnet new webapp -o aspnetcoreapp
    ```

    [!INCLUDE [](~/includes/webapp-alias-notice.md)]

3. Установите доверие к сертификату разработки HTTPS.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    ```console
    dotnet dev-certs https --trust
    ```

   Приведенная выше команда отображает следующее диалоговое окно.

   ![Диалоговое окно "Предупреждение о безопасности"](_static/cert.png)

   Выберите **Да**, если согласны доверять сертификату разработки.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

    ```console
    dotnet dev-certs https --trust
    ```

   Приведенная выше команда отображает следующее сообщение.

   *Запрошено доверие к сертификату разработки HTTPS. Если сертификат не является доверенным, выполните следующую команду:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'` *Эта команда может запросить пароль для установки сертификата в системной цепочке ключей.    Пароль:* 

   Введите пароль, если согласны доверять сертификату разработки.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

   <a name="see-the-documentation-for-your-linux-distribution-on-how-to-trust-the-https-development-certificate"></a>Просмотрите документацию по дистрибутиву Linux, чтобы узнать, как установить отношение доверия к сертификату разработки HTTPS.
---

4. Запустите приложение:

    ```console
    cd aspnetcoreapp
    dotnet run
    ```

5. Перейдите по адресу [http://localhost:5001](http://localhost:5001).  Щелкните **Принять**, чтобы принять политику конфиденциальности и использования файлов cookie. Это приложение не хранит персональные данные.

6. Откройте *Pages/About.cshtml* и измените страницу, добавив выделенное исправление.

    [!code-cshtml[](sample/getting-started/about.cshtml?highlight=9)]

7. Перейдите к [http://localhost:5001/About](http://localhost:5001/About) и проверьте, отобразились ли изменения.

[!INCLUDE [next steps](~/includes/getting-started/next-steps.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

1. Установите [!INCLUDE [](~/includes/net-core-sdk-download-link.md)].

2. Создайте новый проект ASP.NET Core.

   Откройте командную оболочку. Введите следующую команду:

    ```console
    dotnet new razor -o aspnetcoreapp
    ```

3. Запустите приложение с помощью следующих команд:

    ```console
    cd aspnetcoreapp
    dotnet run
    ```

4. Перейдите по адресу [http://localhost:5000](http://localhost:5000).

5. Откройте файл *Pages/About.cshtml* и измените страницу, чтобы на ней отображалось сообщение "Hello, world! Время на сервере — @DateTime.Now" :

    [!code-cshtml[](sample/getting-started/about.cshtml?highlight=9&range=1-9)]

6. Перейдите к [http://localhost:5000/About](http://localhost:5000/About) и проверьте изменения.

[!INCLUDE [next steps](~/includes/getting-started/next-steps.md)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

1. Установите **установщик пакета SDK** версии 1.0.4 для .NET Core со [страницы "Все загрузки .NET Core"](https://www.microsoft.com/net/download/all).

2. Создайте папку для нового проекта ASP.NET Core.

   Откройте командную оболочку. Введите следующие команды.

   ```console
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   ```

3. Если на компьютере установлена более поздняя версия пакета SDK, создайте файл *global.json*, чтобы выбрать пакет SDK версии 1.0.4.

   ```json
   {
     "sdk": { "version": "1.0.4" }
   }
   ```

4. Создайте новый проект ASP.NET Core.

   ```console
   dotnet new web
   ```

5. Восстановите пакеты.

    ```console
    dotnet restore
    ```

6. Запустите приложение.

   ```console
   dotnet run
   ```

   При необходимости команда [dotnet run](/dotnet/core/tools/dotnet-run) сначала выполняет сборку приложения.

7. Перейдите по адресу `http://localhost:5000`.

[!INCLUDE [next steps](~/includes/getting-started/next-steps.md)]
::: moniker-end
