---
ms.openlocfilehash: 308938efbedc4618c7b0ca3ea6b2eebc0582da10
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822485"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="cb285-101">Начните с создания базового веб-приложения ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="cb285-101">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="cb285-102">Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект.</span><span class="sxs-lookup"><span data-stu-id="cb285-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="cb285-103">Выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="cb285-103">Run the following command.</span></span>

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. <span data-ttu-id="cb285-104">После создания проекта убедитесь, что он работает, изменив текущий каталог на каталог **графтуториал** и выполнив следующую команду в командной системе CLI.</span><span class="sxs-lookup"><span data-stu-id="cb285-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="cb285-105">Откройте браузер и перейдите по адресу `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="cb285-105">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="cb285-106">Если все работает, вы должны увидеть основную страницу ASP.NET по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cb285-106">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cb285-107">Если вы получаете предупреждение о том, что сертификат для **localhost** не является доверенным, вы можете установить и доверять сертификату разработки с помощью инфраструктуры .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb285-107">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="cb285-108">Инструкции по работе с определенными операционными системами приведены [в разделе Применение HTTPS в ядре ASP.NET](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) .</span><span class="sxs-lookup"><span data-stu-id="cb285-108">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="cb285-109">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="cb285-109">Add NuGet packages</span></span>

<span data-ttu-id="cb285-110">Прежде чем переходить, установите некоторые дополнительные пакеты NuGet, которые будут использоваться позже.</span><span class="sxs-lookup"><span data-stu-id="cb285-110">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="cb285-111">[Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) для запроса и управления маркерами доступа.</span><span class="sxs-lookup"><span data-stu-id="cb285-111">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="cb285-112">[Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) для добавления пакета SDK Microsoft Graph с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="cb285-112">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding the Microsoft Graph SDK via dependency injection.</span></span>
- <span data-ttu-id="cb285-113">[Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) для входа в систему и для пользовательского интерфейса выхода.</span><span class="sxs-lookup"><span data-stu-id="cb285-113">[Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) for sign-in and sign-out UI.</span></span>
- <span data-ttu-id="cb285-114">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) для совершения звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cb285-114">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="cb285-115">[Тимезонеконвертер](https://github.com/mj1856/TimeZoneConverter) для обработки многоплатформенных идентификаторов с часовым поясом.</span><span class="sxs-lookup"><span data-stu-id="cb285-115">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for handling time zoned identifiers cross-platform.</span></span>

1. <span data-ttu-id="cb285-116">Выполните следующие команды в интерфейсе командной строки, чтобы установить зависимости.</span><span class="sxs-lookup"><span data-stu-id="cb285-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Identity.Web.UI --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.18.0
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="cb285-117">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="cb285-117">Design the app</span></span>

<span data-ttu-id="cb285-118">В этом разделе вы создадите базовую структуру пользовательского интерфейса приложения.</span><span class="sxs-lookup"><span data-stu-id="cb285-118">In this section you will create the basic UI structure of the application.</span></span>

### <a name="implement-alert-extension-methods"></a><span data-ttu-id="cb285-119">Реализация методов расширения оповещений</span><span class="sxs-lookup"><span data-stu-id="cb285-119">Implement alert extension methods</span></span>

<span data-ttu-id="cb285-120">В этом разделе вы создадите методы расширения для типа, `IActionResult` возвращаемого представлениями контроллера.</span><span class="sxs-lookup"><span data-stu-id="cb285-120">In this section you will create extension methods for the `IActionResult` type returned by controller views.</span></span> <span data-ttu-id="cb285-121">Это расширение позволит передавать в представление временные сообщения об ошибках или успешных попыток.</span><span class="sxs-lookup"><span data-stu-id="cb285-121">This extension will enable passing temporary error or success messages to the view.</span></span>

> [!TIP]
> <span data-ttu-id="cb285-122">Для редактирования исходных файлов в этом руководстве можно использовать любой текстовый редактор.</span><span class="sxs-lookup"><span data-stu-id="cb285-122">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="cb285-123">Тем не менее, [Visual Studio Code](https://code.visualstudio.com/) предоставляет дополнительные функции, такие как отладка и IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="cb285-123">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="cb285-124">Создайте новый каталог в каталоге **графтуториал** с именем **Alerts**.</span><span class="sxs-lookup"><span data-stu-id="cb285-124">Create a new directory in the **GraphTutorial** directory named **Alerts**.</span></span>

1. <span data-ttu-id="cb285-125">Создайте новый файл с именем **WithAlertResult.CS** в каталоге **./алертс** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="cb285-125">Create a new file named **WithAlertResult.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. <span data-ttu-id="cb285-126">Создайте новый файл с именем **AlertExtensions.CS** в каталоге **./алертс** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="cb285-126">Create a new file named **AlertExtensions.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a><span data-ttu-id="cb285-127">Реализация методов расширения пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="cb285-127">Implement user data extension methods</span></span>

<span data-ttu-id="cb285-128">В этом разделе вы создадите методы расширения для объекта, `ClaimsPrincipal` созданного платформой удостоверений Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="cb285-128">In this section you will create extension methods for the `ClaimsPrincipal` object generated by the Microsoft Identity platform.</span></span> <span data-ttu-id="cb285-129">Это позволит расширить существующую идентификацию пользователя с помощью данных из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cb285-129">This will allow you to extend the existing user identity with data from Microsoft Graph.</span></span>

> [!NOTE]
> <span data-ttu-id="cb285-130">Этот код — это просто заполнитель, который будет выполнен в более позднее время.</span><span class="sxs-lookup"><span data-stu-id="cb285-130">This code is just a placeholder for now, you will complete it in a later section.</span></span>

1. <span data-ttu-id="cb285-131">Создайте новый каталог в каталоге **графтуториал** с именем **Graph**.</span><span class="sxs-lookup"><span data-stu-id="cb285-131">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>

1. <span data-ttu-id="cb285-132">Создайте новый файл с именем **GraphClaimsPrincipalExtensions.CS** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="cb285-132">Create a new file named **GraphClaimsPrincipalExtensions.cs** and add the following code.</span></span>

    ```csharp
    using System.Security.Claims;

    namespace GraphTutorial
    {
        public static class GraphClaimTypes {
            public const string DisplayName ="graph_name";
            public const string Email = "graph_email";
            public const string Photo = "graph_photo";
            public const string TimeZone = "graph_timezone";
            public const string DateTimeFormat = "graph_datetimeformat";
        }

        // Helper methods to access Graph user data stored in
        // the claims principal
        public static class GraphClaimsPrincipalExtensions
        {
            public static string GetUserGraphDisplayName(this ClaimsPrincipal claimsPrincipal)
            {
                return "Adele Vance";
            }

            public static string GetUserGraphEmail(this ClaimsPrincipal claimsPrincipal)
            {
                return "adelev@contoso.com";
            }

            public static string GetUserGraphPhoto(this ClaimsPrincipal claimsPrincipal)
            {
                return "/img/no-profile-photo.png";
            }
        }
    }
    ```

### <a name="create-views"></a><span data-ttu-id="cb285-133">Создание представлений</span><span class="sxs-lookup"><span data-stu-id="cb285-133">Create views</span></span>

<span data-ttu-id="cb285-134">В этом разделе будет реализовано представление Razor для приложения.</span><span class="sxs-lookup"><span data-stu-id="cb285-134">In this section you will implement the Razor views for the application.</span></span>

1. <span data-ttu-id="cb285-135">Добавьте новый файл с именем **_LoginPartial. cshtml** в каталоге **./виевс/Шаред** и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="cb285-135">Add a new file named **_LoginPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. <span data-ttu-id="cb285-136">Добавьте новый файл с именем **_AlertPartial. cshtml** в каталоге **./виевс/Шаред** и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="cb285-136">Add a new file named **_AlertPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. <span data-ttu-id="cb285-137">Откройте файл **./виевс/шаред/_layout. cshtml** и замените все его содержимое приведенным ниже кодом, чтобы обновить глобальную структуру приложения.</span><span class="sxs-lookup"><span data-stu-id="cb285-137">Open the **./Views/Shared/_Layout.cshtml** file, and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. <span data-ttu-id="cb285-138">Откройте файл **./ввврут/КСС/Сите.КСС** и добавьте приведенный ниже код в конец файла.</span><span class="sxs-lookup"><span data-stu-id="cb285-138">Open **./wwwroot/css/site.css** and add the following code at the bottom of the file.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="cb285-139">Откройте файл **./виевс/Хоме/индекс.кштмл** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="cb285-139">Open the **./Views/Home/index.cshtml** file and replace its contents with the following.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. <span data-ttu-id="cb285-140">Создайте новый каталог в каталоге **./ввврут** с именем **img**.</span><span class="sxs-lookup"><span data-stu-id="cb285-140">Create a new directory in the **./wwwroot** directory named **img**.</span></span> <span data-ttu-id="cb285-141">Добавьте в этот каталог файл изображения с именем **no-profile-photo.png** .</span><span class="sxs-lookup"><span data-stu-id="cb285-141">Add an image file of your choosing named **no-profile-photo.png** in this directory.</span></span> <span data-ttu-id="cb285-142">Это изображение будет использоваться в качестве фотографии пользователя, когда у пользователя нет фотографии в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cb285-142">This image will be used as the user's photo when the user has no photo in Microsoft Graph.</span></span>

    > [!TIP]
    > <span data-ttu-id="cb285-143">Вы можете скачать изображение, используемое на этих снимках экрана, из [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).</span><span class="sxs-lookup"><span data-stu-id="cb285-143">You can download the image used in these screenshots from [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).</span></span>

1. <span data-ttu-id="cb285-144">Сохраните все изменения и перезапустите сервер ( `dotnet run` ).</span><span class="sxs-lookup"><span data-stu-id="cb285-144">Save all of your changes and restart the server (`dotnet run`).</span></span> <span data-ttu-id="cb285-145">Теперь приложение должно выглядеть по-другому.</span><span class="sxs-lookup"><span data-stu-id="cb285-145">Now, the app should look very different.</span></span>

    ![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
