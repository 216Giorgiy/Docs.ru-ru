---
title: "Реализация WebListener веб-сервера в ASP.NET Core"
author: rick-anderson
description: "Вводит WebListener веб-сервер для ASP.NET Core в Windows. Основанное на драйвер режима ядра Http.Sys, WebListener является альтернативой Kestrel, который можно использовать для прямого подключения к Интернету, без IIS."
keywords: "ASP.NET Core, WebListener, HttpListener, префиксы URL-адрес, протокол SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/weblistener
ms.openlocfilehash: bcd225875cfe2a544581c331231c704094780ea3
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2017
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="b5897-105">Реализация WebListener веб-сервера в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5897-105">WebListener web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="b5897-106">По [Tom Dykstra](http://github.com/tdykstra) и [Ross Крис](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="b5897-106">By [Tom Dykstra](http://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="b5897-107">Этот раздел относится только к ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="b5897-107">This topic applies only to ASP.NET Core 1.x.</span></span> <span data-ttu-id="b5897-108">В ASP.NET Core 2.0, называется WebListener [HTTP.sys](httpsys.md).</span><span class="sxs-lookup"><span data-stu-id="b5897-108">In ASP.NET Core 2.0, WebListener is named [HTTP.sys](httpsys.md).</span></span>

<span data-ttu-id="b5897-109">— WebListener [веб-сервер для ASP.NET Core](index.md) , которое будет выполняться только в Windows.</span><span class="sxs-lookup"><span data-stu-id="b5897-109">WebListener is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="b5897-110">Она была основана на [драйвер режима ядра Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="b5897-110">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="b5897-111">WebListener является альтернативой [Kestrel](kestrel.md) , можно использовать для прямого подключения к Интернету без использования IIS в качестве обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="b5897-111">WebListener is an alternative to [Kestrel](kestrel.md) that can be used for direct connection to the Internet without relying on IIS as a reverse proxy server.</span></span> <span data-ttu-id="b5897-112">На самом деле **WebListener не может использоваться с IIS или IIS Express, как оно не совместимо с [модуль ASP.NET Core](aspnet-core-module.md).**</span><span class="sxs-lookup"><span data-stu-id="b5897-112">In fact, **WebListener can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="b5897-113">Несмотря на то, что WebListener был разработан для ASP.NET Core, он может использоваться непосредственно в любом приложении .NET Core или .NET Framework, через [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="b5897-113">Although WebListener was developed for ASP.NET Core, it can be used directly in any .NET Core or .NET Framework application via the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

<span data-ttu-id="b5897-114">WebListener поддерживает следующие функции:</span><span class="sxs-lookup"><span data-stu-id="b5897-114">WebListener supports the following features:</span></span>

- [<span data-ttu-id="b5897-115">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="b5897-115">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="b5897-116">Совместное использование порта</span><span class="sxs-lookup"><span data-stu-id="b5897-116">Port sharing</span></span>
- <span data-ttu-id="b5897-117">HTTPS с сетевого Адаптера</span><span class="sxs-lookup"><span data-stu-id="b5897-117">HTTPS with SNI</span></span>
- <span data-ttu-id="b5897-118">HTTP/2 через TLS (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="b5897-118">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="b5897-119">Файл прямой передачи данных</span><span class="sxs-lookup"><span data-stu-id="b5897-119">Direct file transmission</span></span>
- <span data-ttu-id="b5897-120">Кэширование ответов</span><span class="sxs-lookup"><span data-stu-id="b5897-120">Response caching</span></span>
- <span data-ttu-id="b5897-121">WebSockets (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="b5897-121">WebSockets (Windows 8)</span></span>

<span data-ttu-id="b5897-122">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="b5897-122">Supported Windows versions:</span></span>

- <span data-ttu-id="b5897-123">Windows 7 и Windows Server 2008 R2 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="b5897-123">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="b5897-124">Просмотреть или скачать образец кода</span><span class="sxs-lookup"><span data-stu-id="b5897-124">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)

## <a name="when-to-use-weblistener"></a><span data-ttu-id="b5897-125">Когда следует использовать WebListener</span><span class="sxs-lookup"><span data-stu-id="b5897-125">When to use WebListener</span></span>

<span data-ttu-id="b5897-126">WebListener полезен для развертываний, где нужно предоставить сервера к Интернету напрямую без использования служб IIS.</span><span class="sxs-lookup"><span data-stu-id="b5897-126">WebListener is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![Weblistener взаимодействует непосредственно с Интернетом](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="b5897-128">Так как она была основана на Http.Sys, WebListener не требует обратного прокси-сервера для защиты от атак на систему.</span><span class="sxs-lookup"><span data-stu-id="b5897-128">Because it's built on Http.Sys, WebListener doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="b5897-129">Компонент Http.Sys — это надежная технология, которое защищает от атак многих типов, а также обеспечивает надежности, безопасности и масштабируемости полнофункциональное веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="b5897-129">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="b5897-130">Сами службы IIS выполняется как HTTP-прослушивателем на базе Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="b5897-130">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span> 

<span data-ttu-id="b5897-131">WebListener при также хорошо подходит для развертываний внутренней требуется одна из возможностей, которой он обеспечивает, что не удается получить с помощью Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b5897-131">WebListener is also a good choice for internal deployments when you need one of the features it offers that you can't get by using Kestrel.</span></span>

![Weblistener взаимодействует непосредственно с внутренней сети](weblistener/_static/weblistener-to-internal.png)

## <a name="how-to-use-weblistener"></a><span data-ttu-id="b5897-133">Как использовать WebListener</span><span class="sxs-lookup"><span data-stu-id="b5897-133">How to use WebListener</span></span>

<span data-ttu-id="b5897-134">Ниже приведен обзор задач установки для ОС и приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5897-134">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="b5897-135">Настройка Windows Server</span><span class="sxs-lookup"><span data-stu-id="b5897-135">Configure Windows Server</span></span>

* <span data-ttu-id="b5897-136">Установите версию .NET с требованиями приложения, такие как [.NET Core](https://go.microsoft.com/fwlink/?LinkID=827524) или .NET Framework 4.5.1.</span><span class="sxs-lookup"><span data-stu-id="b5897-136">Install the version of .NET that your application requires, such as [.NET Core](https://go.microsoft.com/fwlink/?LinkID=827524) or .NET Framework 4.5.1.</span></span>

* <span data-ttu-id="b5897-137">Предварительной регистрации префиксы URL-адрес, привязку к WebListener и настроить сертификаты SSL</span><span class="sxs-lookup"><span data-stu-id="b5897-137">Preregister URL prefixes to bind to WebListener, and set up SSL certificates</span></span>

   <span data-ttu-id="b5897-138">Если не предварительной регистрации URL-префиксы в Windows, необходимо запустить приложение с правами администратора.</span><span class="sxs-lookup"><span data-stu-id="b5897-138">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="b5897-139">Единственное исключение — если привязать к локальному компьютеру с помощью HTTP (не HTTPS) с номерами выше 1024; номер порта в этом случае правами администратора, не являются обязательными.</span><span class="sxs-lookup"><span data-stu-id="b5897-139">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case administrator privileges aren't required.</span></span>

   <span data-ttu-id="b5897-140">Дополнительные сведения см. в разделе [предварительной регистрации префиксов и настройке SSL](#preregister-url-prefixes-and-configure-ssl) далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="b5897-140">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="b5897-141">Открыть порты брандмауэра, разрешающее трафик для достижения WebListener.</span><span class="sxs-lookup"><span data-stu-id="b5897-141">Open firewall ports to allow traffic to reach WebListener.</span></span>

   <span data-ttu-id="b5897-142">Можно использовать netsh.exe или [командлеты PowerShell](https://technet.microsoft.com/library/jj554906).</span><span class="sxs-lookup"><span data-stu-id="b5897-142">You can use netsh.exe or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="b5897-143">Существуют также [параметры реестра Http.Sys](https://support.microsoft.com/kb/820129).</span><span class="sxs-lookup"><span data-stu-id="b5897-143">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application"></a><span data-ttu-id="b5897-144">Настройка приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5897-144">Configure your ASP.NET Core application</span></span>

* <span data-ttu-id="b5897-145">Установка пакета NuGet [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span><span class="sxs-lookup"><span data-stu-id="b5897-145">Install the NuGet package [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span></span> <span data-ttu-id="b5897-146">Также устанавливает [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) как зависимость.</span><span class="sxs-lookup"><span data-stu-id="b5897-146">This also installs [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) as a dependency.</span></span>

* <span data-ttu-id="b5897-147">Вызовите [ `UseWebListener` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilderKestrelExtensions/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilderWebListenerExtensions.UseWebListener.md) метод расширения в [WebHostBuilder](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilder/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilder.md) в ваш `Main` метод, указывая любой WebListener [параметры](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) и [ Параметры](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) , требуется, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="b5897-147">Call the [`UseWebListener`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilderKestrelExtensions/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilderWebListenerExtensions.UseWebListener.md) extension method on [WebHostBuilder](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilder/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilder.md) in your `Main` method, specifying any WebListener [options](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) and [settings](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* <span data-ttu-id="b5897-148">Настройка URL-адреса и порты прослушивания</span><span class="sxs-lookup"><span data-stu-id="b5897-148">Configure URLs and ports to listen on</span></span> 

  <span data-ttu-id="b5897-149">По умолчанию ASP.NET Core привязывается к `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b5897-149">By default ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b5897-150">Чтобы настроить префиксы URL-адреса и порты, можно использовать `UseURLs` метод расширения `urls` аргумент командной строки или система конфигурации ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5897-150">To configure URL prefixes and ports, you can use the `UseURLs` extension method, the `urls` command-line argument or the ASP.NET Core configuration system.</span></span> <span data-ttu-id="b5897-151">Дополнительные сведения см. в разделе [размещения](../../fundamentals/hosting.md).</span><span class="sxs-lookup"><span data-stu-id="b5897-151">For more information, see [Hosting](../../fundamentals/hosting.md).</span></span>

  <span data-ttu-id="b5897-152">Веб-прослушиватель использует [Http.Sys префикс строковые форматы](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span><span class="sxs-lookup"><span data-stu-id="b5897-152">Web Listener uses the [Http.Sys prefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span> <span data-ttu-id="b5897-153">Требования к формат строки префикса, относящиеся к WebListener отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="b5897-153">There are no prefix string format requirements that are specific to WebListener.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b5897-154">Убедитесь, что указан одинаковые строки префикса в `UseUrls` , вы предварительной регистрации на сервере.</span><span class="sxs-lookup"><span data-stu-id="b5897-154">Make sure that you specify the same prefix strings in `UseUrls` that you preregister on the server.</span></span> 

* <span data-ttu-id="b5897-155">Убедитесь, что приложение не настроено для запуска служб IIS или IIS Express.</span><span class="sxs-lookup"><span data-stu-id="b5897-155">Make sure your application is not configured to run IIS or IIS Express.</span></span>

  <span data-ttu-id="b5897-156">В Visual Studio для IIS Express указан профиль запуска по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b5897-156">In Visual Studio, the default launch profile is for IIS Express.</span></span>  <span data-ttu-id="b5897-157">Чтобы запустить проект как консольное приложение необходимо вручную изменить выбранного профиля, как показано на следующем снимке экрана.</span><span class="sxs-lookup"><span data-stu-id="b5897-157">To run the project as a console application you have to manually change the selected profile, as shown in the following screen shot.</span></span>

  ![Выберите профиль приложения консоли](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a><span data-ttu-id="b5897-159">Как использовать WebListener за пределами ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5897-159">How to use WebListener outside of ASP.NET Core</span></span>

* <span data-ttu-id="b5897-160">Установка [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="b5897-160">Install the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

* <span data-ttu-id="b5897-161">[Префиксы URL-адрес, привязку к WebListener и настроить сертификаты SSL предварительной регистрации](#preregister-url-prefixes-and-configure-ssl) как для использования в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5897-161">[Preregister URL prefixes to bind to WebListener, and set up SSL certificates](#preregister-url-prefixes-and-configure-ssl) as you would for use in ASP.NET Core.</span></span>

<span data-ttu-id="b5897-162">Существуют также [параметры реестра Http.Sys](https://support.microsoft.com/kb/820129).</span><span class="sxs-lookup"><span data-stu-id="b5897-162">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>


<span data-ttu-id="b5897-163">Ниже приведен пример кода, демонстрирующий применение WebListener за пределами ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b5897-163">Here's a code sample that demonstrates WebListener use outside of ASP.NET Core:</span></span>

```csharp
var settings = new WebListenerSettings();
settings.UrlPrefixes.Add("http://localhost:8080");

using (WebListener listener = new WebListener(settings))
{
    listener.Start();

    while (true)
    {
        var context = await listener.AcceptAsync();
        byte[] bytes = Encoding.ASCII.GetBytes("Hello World: " + DateTime.Now);
        context.Response.ContentLength = bytes.Length;
        context.Response.ContentType = "text/plain";

        await context.Response.Body.WriteAsync(bytes, 0, bytes.Length);
        context.Dispose();
    }
}
```

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="b5897-164">Предварительной регистрации префиксы URL-адреса и настроить протокол SSL</span><span class="sxs-lookup"><span data-stu-id="b5897-164">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="b5897-165">Службы IIS и WebListener основаны на базовый драйвер режима ядра Http.Sys для прослушивания запросов и первоначального обработки.</span><span class="sxs-lookup"><span data-stu-id="b5897-165">Both IIS and WebListener rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="b5897-166">В службах IIS пользовательский Интерфейс управления позволяет сравнительно легко настраивать все.</span><span class="sxs-lookup"><span data-stu-id="b5897-166">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="b5897-167">Если вы используете WebListener необходимо настроить Http.Sys самостоятельно.</span><span class="sxs-lookup"><span data-stu-id="b5897-167">However, if you're using WebListener you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="b5897-168">Встроенное средство выполнения этих процедур является netsh.exe.</span><span class="sxs-lookup"><span data-stu-id="b5897-168">The built-in tool for doing that is netsh.exe.</span></span> 

<span data-ttu-id="b5897-169">Наиболее распространенные задачи, необходимо использовать netsh.exe для резервирования URL-префиксы и назначение SSL-сертификаты.</span><span class="sxs-lookup"><span data-stu-id="b5897-169">The most common tasks you need to use netsh.exe for are reserving URL prefixes and assigning SSL certificates.</span></span>

<span data-ttu-id="b5897-170">NetSh.exe не удобное средство для начинающих.</span><span class="sxs-lookup"><span data-stu-id="b5897-170">NetSh.exe is not an easy tool to use for beginners.</span></span> <span data-ttu-id="b5897-171">Ниже приведен пример минимальный набор необходимых для резервирования URL-префиксы для портов 80 и 443.</span><span class="sxs-lookup"><span data-stu-id="b5897-171">The following example shows the bare minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="b5897-172">Приведенный ниже показано, как назначить SSL-сертификата:</span><span class="sxs-lookup"><span data-stu-id="b5897-172">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}".
```

<span data-ttu-id="b5897-173">Ниже приведен в официальной документации.</span><span class="sxs-lookup"><span data-stu-id="b5897-173">Here is the official reference documentation:</span></span>

* [<span data-ttu-id="b5897-174">Команды Netsh для гипертекста передачи протокол (HTTP)</span><span class="sxs-lookup"><span data-stu-id="b5897-174">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](http://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="b5897-175">UrlPrefix строк</span><span class="sxs-lookup"><span data-stu-id="b5897-175">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="b5897-176">Подробные инструкции для нескольких сценариев на следующих ресурсах.</span><span class="sxs-lookup"><span data-stu-id="b5897-176">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="b5897-177">Статьи, которые ссылаются на `HttpListener` применяются как к `WebListener`, как они строятся на Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="b5897-177">Articles that refer to `HttpListener` apply equally to `WebListener`, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="b5897-178">Как: Настройка порта с SSL-сертификата</span><span class="sxs-lookup"><span data-stu-id="b5897-178">How to: Configure a Port with an SSL Certificate</span></span>](http://msdn.microsoft.com/library/ms733791.aspx)
* <span data-ttu-id="b5897-179">[Подключения по протоколу HTTPS - HttpListener и на основе размещения сертификацию клиента](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) это является блог сторонних разработчиков и довольно старый, но по-прежнему содержит полезные сведения.</span><span class="sxs-lookup"><span data-stu-id="b5897-179">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="b5897-180">[Практическое руководство: Пошаговое руководство с помощью HttpListener или HTTP-сервере неуправляемого типов кода (C++) как простой сервера SSL](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) это тоже старые блог полезные сведения.</span><span class="sxs-lookup"><span data-stu-id="b5897-180">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) This too is an older blog with useful information.</span></span>
* [<span data-ttu-id="b5897-181">Как настроить WebListener Core .NET с помощью протокола SSL?</span><span class="sxs-lookup"><span data-stu-id="b5897-181">How Do I Set Up A .NET Core WebListener With SSL?</span></span>](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

<span data-ttu-id="b5897-182">Ниже приведены некоторые сторонних средств, которые могут быть проще в использовании, чем netsh.exe командной строки.</span><span class="sxs-lookup"><span data-stu-id="b5897-182">Here are some third-party tools that can be easier to use than the netsh.exe command line.</span></span> <span data-ttu-id="b5897-183">Они не предоставляется или не поддерживается корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="b5897-183">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="b5897-184">Средства Запуск от имени администратора по умолчанию, поскольку сам netsh.exe требуются права администратора.</span><span class="sxs-lookup"><span data-stu-id="b5897-184">The tools run as administrator by default, since netsh.exe itself requires administrator privileges.</span></span>

* <span data-ttu-id="b5897-185">[HTTP.sys Manager](http://httpsysmanager.codeplex.com/) предоставляет пользовательский Интерфейс для включения в список и Настройка SSL-сертификатов и параметров, префиксов резервирования и списки доверия сертификатов.</span><span class="sxs-lookup"><span data-stu-id="b5897-185">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="b5897-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) позволяет перечислить или настроить сертификаты SSL и префиксы URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="b5897-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="b5897-187">Пользовательский Интерфейс будет более http.sys Manager предоставляет несколько дополнительных параметров конфигурации, а в противном случае она предоставляет аналогичные функциональные возможности.</span><span class="sxs-lookup"><span data-stu-id="b5897-187">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="b5897-188">Не удается создать новый список доверия сертификатов (CTL), но можно назначить уже существующую.</span><span class="sxs-lookup"><span data-stu-id="b5897-188">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

<span data-ttu-id="b5897-189">Для создания самозаверяющего SSL-сертификаты, корпорация Майкрософт предоставляет средства командной строки: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) и командлет PowerShell [New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633).</span><span class="sxs-lookup"><span data-stu-id="b5897-189">For generating self-signed SSL certificates, Microsoft provides command-line tools: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) and the PowerShell cmdlet [New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633).</span></span> <span data-ttu-id="b5897-190">Существуют также сторонние пользовательского интерфейса средства, облегчающие создавать самозаверяющие сертификаты SSL:</span><span class="sxs-lookup"><span data-stu-id="b5897-190">There are also third-party UI tools that make it easier for you to generate self-signed SSL certificates:</span></span>

* [<span data-ttu-id="b5897-191">SelfCert</span><span class="sxs-lookup"><span data-stu-id="b5897-191">SelfCert</span></span>](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [<span data-ttu-id="b5897-192">Makecert пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="b5897-192">Makecert UI</span></span>](http://makecertui.codeplex.com/)

## <a name="next-steps"></a><span data-ttu-id="b5897-193">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="b5897-193">Next steps</span></span>

<span data-ttu-id="b5897-194">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="b5897-194">For more information, see the following resources:</span></span>

* [<span data-ttu-id="b5897-195">Образец приложения для этой статьи</span><span class="sxs-lookup"><span data-stu-id="b5897-195">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [<span data-ttu-id="b5897-196">WebListener исходного кода</span><span class="sxs-lookup"><span data-stu-id="b5897-196">WebListener source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="b5897-197">Размещение</span><span class="sxs-lookup"><span data-stu-id="b5897-197">Hosting</span></span>](../hosting.md)