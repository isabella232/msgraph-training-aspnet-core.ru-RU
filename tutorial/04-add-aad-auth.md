---
ms.openlocfilehash: 6e6c476b4ff0901f50d8e35a17f584d73b48b533
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822474"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова API Microsoft Graph. На этом этапе вы настроите библиотеку [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) .

> [!IMPORTANT]
> Чтобы не хранить идентификатор и секрет приложения в источнике, вы будете использовать [Диспетчер секрета .NET](/aspnet/core/security/app-secrets) для хранения этих значений. Диспетчер секретности предназначен только для целей разработки, рабочие приложения должны использовать доверенный диспетчер секретов для хранения секретов.

1. Откройте **./appsettings.js** и замените его содержимое приведенным ниже содержимым.

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. Откройте службу CLI в каталоге, где находится **графтуториал. csproj** , и выполните следующие команды, заменив `YOUR_APP_ID` идентификатор приложения на портале Azure и `YOUR_APP_SECRET` указав секрет вашего приложения.

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a>Реализация входа

Для начала добавьте в приложение службы платформы удостоверений Майкрософт.

1. Создайте новый файл с именем **GraphConstants.CS** в каталоге **./граф** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. Откройте файл **./Startup.CS** и добавьте приведенные ниже `using` операторы в начало файла.

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

1. Замените имеющуюся функцию `ConfigureServices` указанным ниже кодом.

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

1. В `Configure` функции добавьте указанную ниже строку `app.UseAuthorization();` .

    ```csharp
    app.UseAuthentication();
    ```

1. Откройте **/контроллерс/хомеконтроллер.КС** и замените его содержимое приведенным ниже.

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

1. Сохраните изменения и запустите проект. Войдите с помощью учетной записи Майкрософт.

1. Проверьте запрос согласия. Список разрешений соответствует списку областей разрешений, настроенных в **./граф/графконстантс.КС**.

    - **Поддержка доступа к данным, доступ к которым предоставлен:** ( `offline_access` ) это разрешение запрашивается с помощью MSAL для получения маркеров обновления.
    - **Вход и чтение профиля:** ( `User.Read` ) это разрешение позволяет приложению получать профиль пользователя, вошедшего в систему, и фотографию профиля.
    - **Считывание параметров почтового ящика:** ( `MailboxSettings.Read` ) это разрешение позволяет приложению считывать параметры почтового ящика пользователя, в том числе часовой пояс и формат времени.
    - **Полный доступ к Вашим календарям:** ( `Calendars.ReadWrite` ) это разрешение позволяет приложению считывать события в календаре пользователя, добавлять новые события и изменять существующие.

    ![Снимок экрана с запросом о предоставлении платформы идентификации Майкрософт](./images/add-aad-auth-03.png)

    Дополнительные сведения о согласии можно найти в статье Общие сведения о [согласии приложений Azure AD](/azure/active-directory/develop/application-consent-experience).

1. Согласие на запрошенные разрешения. Браузер перенаправляется на приложение, отображая маркер.

### <a name="get-user-details"></a>Получение сведений о пользователе

Когда пользователь входит в систему, вы можете получить сведения из Microsoft Graph.

1. Откройте **/граф/графклаимспринЦипалекстенсионс.КС** и замените все содержимое следующим.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. Откройте **./Startup.CS** и замените существующую `.AddMicrosoftIdentityWebApp(Configuration)` строку приведенным ниже кодом.

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    Рассмотрите, что делает этот код.

    - Добавляет обработчик для `OnTokenValidated` события.
        - Он использует `ITokenAcquisition` интерфейс для получения маркера доступа.
        - Он вызывает Microsoft Graph, чтобы получить профиль пользователя и фотографию.
        - Он добавляет данные Graph в удостоверение пользователя.

1. Добавьте следующий вызов функции после `EnableTokenAcquisitionToCallDownstreamApi` вызова и перед `AddInMemoryTokenCaches` вызовом.

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    Это сделает **GraphServiceClient** доступным для контроллеров с помощью внедрения зависимостей.

1. Откройте **/контроллерс/хомеконтроллер.КС** и замените `Index` функцию на приведенную ниже строку.

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. Удалите все ссылки `ITokenAcquisition` в классе **HomeController** .

1. Сохраните изменения, запустите приложение и пройдите процесс входа. Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.

    ![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

1. Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** . При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.

    ![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Хранение и обновление маркеров

На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер кратковременно используется. Срок действия маркера истечет через час после его выдачи. В этом случае маркер обновления становится полезен. Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.

Так как приложение использует библиотеку Microsoft. Identity. Web, не требуется реализовывать логику хранения или обновления маркеров.

Приложение использует кэш маркеров в памяти, необходимый для приложений, которые не нуждаются в сохранении маркеров при перезапуске приложения. Рабочие приложения вместо этого могут использовать [параметры распределенного кэша](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) в библиотеке Microsoft. Identity. Web.

`GetAccessTokenForUserAsync`Метод обрабатывает истечение срока действия и обновления токена. Сначала он проверяет кэшированный маркер и, если срок его действия не истек, он возвращает его. Если срок действия истек, он использует кэшированный маркер обновления, чтобы получить новый.

**GraphServiceClient** , к которым контроллеры получают через внедрение зависимости, будут предварительно настроены с помощью используемого поставщика проверки подлинности `GetAccessTokenForUserAsync` .
