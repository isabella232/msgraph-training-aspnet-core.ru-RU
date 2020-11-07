---
ms.openlocfilehash: 6e6c476b4ff0901f50d8e35a17f584d73b48b533
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822474"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="da63c-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="da63c-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="da63c-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="da63c-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="da63c-103">На этом этапе вы настроите библиотеку [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) .</span><span class="sxs-lookup"><span data-stu-id="da63c-103">In this step you will configure the [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) library.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="da63c-104">Чтобы не хранить идентификатор и секрет приложения в источнике, вы будете использовать [Диспетчер секрета .NET](/aspnet/core/security/app-secrets) для хранения этих значений.</span><span class="sxs-lookup"><span data-stu-id="da63c-104">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="da63c-105">Диспетчер секретности предназначен только для целей разработки, рабочие приложения должны использовать доверенный диспетчер секретов для хранения секретов.</span><span class="sxs-lookup"><span data-stu-id="da63c-105">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

1. <span data-ttu-id="da63c-106">Откройте **./appsettings.js** и замените его содержимое приведенным ниже содержимым.</span><span class="sxs-lookup"><span data-stu-id="da63c-106">Open **./appsettings.json** and replace its contents with the following.</span></span>

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. <span data-ttu-id="da63c-107">Откройте службу CLI в каталоге, где находится **графтуториал. csproj** , и выполните следующие команды, заменив `YOUR_APP_ID` идентификатор приложения на портале Azure и `YOUR_APP_SECRET` указав секрет вашего приложения.</span><span class="sxs-lookup"><span data-stu-id="da63c-107">Open your CLI in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="da63c-108">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="da63c-108">Implement sign-in</span></span>

<span data-ttu-id="da63c-109">Для начала добавьте в приложение службы платформы удостоверений Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="da63c-109">Start by adding the Microsoft Identity platform services to the application.</span></span>

1. <span data-ttu-id="da63c-110">Создайте новый файл с именем **GraphConstants.CS** в каталоге **./граф** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="da63c-110">Create a new file named **GraphConstants.cs** in the **./Graph** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. <span data-ttu-id="da63c-111">Откройте файл **./Startup.CS** и добавьте приведенные ниже `using` операторы в начало файла.</span><span class="sxs-lookup"><span data-stu-id="da63c-111">Open the **./Startup.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Microsoft.AspNetCore.Authentication.OpenIdConnect;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc.Authorization;
    using Microsoft.Identity.Web;
    using Microsoft.Identity.Web.UI;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.Graph;
    using System.Net;
    using System.Net.Http.Headers;
    ```

1. <span data-ttu-id="da63c-112">Замените имеющуюся функцию `ConfigureServices` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="da63c-112">Replace the existing `ConfigureServices` function with the following.</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services
            // Use OpenId authentication
            .AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
            // Specify this is a web app and needs auth code flow
            .AddMicrosoftIdentityWebApp(Configuration)
            // Add ability to call web API (Graph)
            // and get access tokens
            .EnableTokenAcquisitionToCallDownstreamApi(options => {
                Configuration.Bind("AzureAd", options);
            }, GraphConstants.Scopes)
            // Use in-memory token cache
            // See https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization
            .AddInMemoryTokenCaches();

        // Require authentication
        services.AddControllersWithViews(options =>
        {
            var policy = new AuthorizationPolicyBuilder()
                .RequireAuthenticatedUser()
                .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
        })
        // Add the Microsoft Identity UI pages for signin/out
        .AddMicrosoftIdentityUI();
    }
    ```

1. <span data-ttu-id="da63c-113">В `Configure` функции добавьте указанную ниже строку `app.UseAuthorization();` .</span><span class="sxs-lookup"><span data-stu-id="da63c-113">In the `Configure` function, add the following line above the `app.UseAuthorization();` line.</span></span>

    ```csharp
    app.UseAuthentication();
    ```

1. <span data-ttu-id="da63c-114">Откройте **/контроллерс/хомеконтроллер.КС** и замените его содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="da63c-114">Open **./Controllers/HomeController.cs** and replace its contents with the following.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using System.Diagnostics;
    using System.Threading.Tasks;

    namespace GraphTutorial.Controllers
    {
        public class HomeController : Controller
        {
            ITokenAcquisition _tokenAcquisition;
            private readonly ILogger<HomeController> _logger;

            // Get the ITokenAcquisition interface via
            // dependency injection
            public HomeController(
                ITokenAcquisition tokenAcquisition,
                ILogger<HomeController> logger)
            {
                _tokenAcquisition = tokenAcquisition;
                _logger = logger;
            }

            public async Task<IActionResult> Index()
            {
                // TEMPORARY
                // Get the token and display it
                try
                {
                    string token = await _tokenAcquisition
                        .GetAccessTokenForUserAsync(GraphConstants.Scopes);
                    return View().WithInfo("Token acquired", token);
                }
                catch (MicrosoftIdentityWebChallengeUserException)
                {
                    return Challenge();
                }
            }

            public IActionResult Privacy()
            {
                return View();
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            public IActionResult Error()
            {
                return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            [AllowAnonymous]
            public IActionResult ErrorWithMessage(string message, string debug)
            {
                return View("Index").WithError(message, debug);
            }
        }
    }
    ```

1. <span data-ttu-id="da63c-115">Сохраните изменения и запустите проект.</span><span class="sxs-lookup"><span data-stu-id="da63c-115">Save your changes and start the project.</span></span> <span data-ttu-id="da63c-116">Войдите с помощью учетной записи Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="da63c-116">Login with your Microsoft account.</span></span>

1. <span data-ttu-id="da63c-117">Проверьте запрос согласия.</span><span class="sxs-lookup"><span data-stu-id="da63c-117">Examine the consent prompt.</span></span> <span data-ttu-id="da63c-118">Список разрешений соответствует списку областей разрешений, настроенных в **./граф/графконстантс.КС**.</span><span class="sxs-lookup"><span data-stu-id="da63c-118">The list of permissions correspond to list of permissions scopes configured in **./Graph/GraphConstants.cs**.</span></span>

    - <span data-ttu-id="da63c-119">**Поддержка доступа к данным, доступ к которым предоставлен:** ( `offline_access` ) это разрешение запрашивается с помощью MSAL для получения маркеров обновления.</span><span class="sxs-lookup"><span data-stu-id="da63c-119">**Maintain access to data you have given it access to:** (`offline_access`) This permission is requested by MSAL in order to retrieve refresh tokens.</span></span>
    - <span data-ttu-id="da63c-120">**Вход и чтение профиля:** ( `User.Read` ) это разрешение позволяет приложению получать профиль пользователя, вошедшего в систему, и фотографию профиля.</span><span class="sxs-lookup"><span data-stu-id="da63c-120">**Sign you in and read your profile:** (`User.Read`) This permission allows the app to get the logged-in user's profile and profile photo.</span></span>
    - <span data-ttu-id="da63c-121">**Считывание параметров почтового ящика:** ( `MailboxSettings.Read` ) это разрешение позволяет приложению считывать параметры почтового ящика пользователя, в том числе часовой пояс и формат времени.</span><span class="sxs-lookup"><span data-stu-id="da63c-121">**Read your mailbox settings:** (`MailboxSettings.Read`) This permission allows the app to read the user's mailbox settings, including time zone and time format.</span></span>
    - <span data-ttu-id="da63c-122">**Полный доступ к Вашим календарям:** ( `Calendars.ReadWrite` ) это разрешение позволяет приложению считывать события в календаре пользователя, добавлять новые события и изменять существующие.</span><span class="sxs-lookup"><span data-stu-id="da63c-122">**Have full access to your calendars:** (`Calendars.ReadWrite`) This permission allows the app to read events on the user's calendar, add new events, and modify existing ones.</span></span>

    ![Снимок экрана с запросом о предоставлении платформы идентификации Майкрософт](./images/add-aad-auth-03.png)

    <span data-ttu-id="da63c-124">Дополнительные сведения о согласии можно найти в статье Общие сведения о [согласии приложений Azure AD](/azure/active-directory/develop/application-consent-experience).</span><span class="sxs-lookup"><span data-stu-id="da63c-124">For more information regarding consent, see [Understanding Azure AD application consent experiences](/azure/active-directory/develop/application-consent-experience).</span></span>

1. <span data-ttu-id="da63c-125">Согласие на запрошенные разрешения.</span><span class="sxs-lookup"><span data-stu-id="da63c-125">Consent to the requested permissions.</span></span> <span data-ttu-id="da63c-126">Браузер перенаправляется на приложение, отображая маркер.</span><span class="sxs-lookup"><span data-stu-id="da63c-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="da63c-127">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="da63c-127">Get user details</span></span>

<span data-ttu-id="da63c-128">Когда пользователь входит в систему, вы можете получить сведения из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="da63c-128">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="da63c-129">Откройте **/граф/графклаимспринЦипалекстенсионс.КС** и замените все содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="da63c-129">Open **./Graph/GraphClaimsPrincipalExtensions.cs** and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. <span data-ttu-id="da63c-130">Откройте **./Startup.CS** и замените существующую `.AddMicrosoftIdentityWebApp(Configuration)` строку приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="da63c-130">Open **./Startup.cs** and replace the existing `.AddMicrosoftIdentityWebApp(Configuration)` line with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    <span data-ttu-id="da63c-131">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="da63c-131">Consider what this code does.</span></span>

    - <span data-ttu-id="da63c-132">Добавляет обработчик для `OnTokenValidated` события.</span><span class="sxs-lookup"><span data-stu-id="da63c-132">It adds an event handler for the `OnTokenValidated` event.</span></span>
        - <span data-ttu-id="da63c-133">Он использует `ITokenAcquisition` интерфейс для получения маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="da63c-133">It uses the `ITokenAcquisition` interface to get an access token.</span></span>
        - <span data-ttu-id="da63c-134">Он вызывает Microsoft Graph, чтобы получить профиль пользователя и фотографию.</span><span class="sxs-lookup"><span data-stu-id="da63c-134">It calls Microsoft Graph to get the user's profile and photo.</span></span>
        - <span data-ttu-id="da63c-135">Он добавляет данные Graph в удостоверение пользователя.</span><span class="sxs-lookup"><span data-stu-id="da63c-135">It adds the Graph information to the user's identity.</span></span>

1. <span data-ttu-id="da63c-136">Добавьте следующий вызов функции после `EnableTokenAcquisitionToCallDownstreamApi` вызова и перед `AddInMemoryTokenCaches` вызовом.</span><span class="sxs-lookup"><span data-stu-id="da63c-136">Add the following function call after the `EnableTokenAcquisitionToCallDownstreamApi` call and before the `AddInMemoryTokenCaches` call.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    <span data-ttu-id="da63c-137">Это сделает **GraphServiceClient** доступным для контроллеров с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="da63c-137">This will make an authenticated **GraphServiceClient** available to controllers via dependency injection.</span></span>

1. <span data-ttu-id="da63c-138">Откройте **/контроллерс/хомеконтроллер.КС** и замените `Index` функцию на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="da63c-138">Open **./Controllers/HomeController.cs** and replace the `Index` function with the following.</span></span>

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. <span data-ttu-id="da63c-139">Удалите все ссылки `ITokenAcquisition` в классе **HomeController** .</span><span class="sxs-lookup"><span data-stu-id="da63c-139">Remove all references to `ITokenAcquisition` in the **HomeController** class.</span></span>

1. <span data-ttu-id="da63c-140">Сохраните изменения, запустите приложение и пройдите процесс входа.</span><span class="sxs-lookup"><span data-stu-id="da63c-140">Save your changes, start the app, and go through the sign-in process.</span></span> <span data-ttu-id="da63c-141">Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.</span><span class="sxs-lookup"><span data-stu-id="da63c-141">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

1. <span data-ttu-id="da63c-143">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="da63c-143">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="da63c-144">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="da63c-144">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="da63c-146">Хранение и обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="da63c-146">Storing and refreshing tokens</span></span>

<span data-ttu-id="da63c-147">На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="da63c-147">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="da63c-148">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="da63c-148">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="da63c-149">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="da63c-149">However, this token is short-lived.</span></span> <span data-ttu-id="da63c-150">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="da63c-150">The token expires an hour after it is issued.</span></span> <span data-ttu-id="da63c-151">В этом случае маркер обновления становится полезен.</span><span class="sxs-lookup"><span data-stu-id="da63c-151">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="da63c-152">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="da63c-152">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="da63c-153">Так как приложение использует библиотеку Microsoft. Identity. Web, не требуется реализовывать логику хранения или обновления маркеров.</span><span class="sxs-lookup"><span data-stu-id="da63c-153">Because the app is using the Microsoft.Identity.Web library, you do not have to implement any token storage or refresh logic.</span></span>

<span data-ttu-id="da63c-154">Приложение использует кэш маркеров в памяти, необходимый для приложений, которые не нуждаются в сохранении маркеров при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="da63c-154">The app uses the in-memory token cache, which is sufficient for apps that do not need to persist tokens when the app restarts.</span></span> <span data-ttu-id="da63c-155">Рабочие приложения вместо этого могут использовать [параметры распределенного кэша](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) в библиотеке Microsoft. Identity. Web.</span><span class="sxs-lookup"><span data-stu-id="da63c-155">Production apps may instead use the [distributed cache options](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in the Microsoft.Identity.Web library.</span></span>

<span data-ttu-id="da63c-156">`GetAccessTokenForUserAsync`Метод обрабатывает истечение срока действия и обновления токена.</span><span class="sxs-lookup"><span data-stu-id="da63c-156">The `GetAccessTokenForUserAsync` method handles token expiration and refresh for you.</span></span> <span data-ttu-id="da63c-157">Сначала он проверяет кэшированный маркер и, если срок его действия не истек, он возвращает его.</span><span class="sxs-lookup"><span data-stu-id="da63c-157">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="da63c-158">Если срок действия истек, он использует кэшированный маркер обновления, чтобы получить новый.</span><span class="sxs-lookup"><span data-stu-id="da63c-158">If it is expired, it uses the cached refresh token to obtain a new one.</span></span>

<span data-ttu-id="da63c-159">**GraphServiceClient** , к которым контроллеры получают через внедрение зависимости, будут предварительно настроены с помощью используемого поставщика проверки подлинности `GetAccessTokenForUserAsync` .</span><span class="sxs-lookup"><span data-stu-id="da63c-159">The **GraphServiceClient** that controllers get via dependency injection will be pre-configured with an authentication provider that uses `GetAccessTokenForUserAsync` for you.</span></span>
