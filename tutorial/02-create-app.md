---
ms.openlocfilehash: 308938efbedc4618c7b0ca3ea6b2eebc0582da10
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822485"
---
<!-- markdownlint-disable MD002 MD041 -->

Начните с создания базового веб-приложения ASP.NET.

1. Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект. Выполните следующую команду.

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. После создания проекта убедитесь, что он работает, изменив текущий каталог на каталог **графтуториал** и выполнив следующую команду в командной системе CLI.

    ```Shell
    dotnet run
    ```

1. Откройте браузер и перейдите по адресу `https://localhost:5001` . Если все работает, вы должны увидеть основную страницу ASP.NET по умолчанию.

> [!IMPORTANT]
> Если вы получаете предупреждение о том, что сертификат для **localhost** не является доверенным, вы можете установить и доверять сертификату разработки с помощью инфраструктуры .NET Core. Инструкции по работе с определенными операционными системами приведены [в разделе Применение HTTPS в ядре ASP.NET](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) .

## <a name="add-nuget-packages"></a>Добавление пакетов NuGet

Прежде чем переходить, установите некоторые дополнительные пакеты NuGet, которые будут использоваться позже.

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) для запроса и управления маркерами доступа.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) для добавления пакета SDK Microsoft Graph с помощью внедрения зависимостей.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) для входа в систему и для пользовательского интерфейса выхода.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) для совершения звонков в Microsoft Graph.
- [Тимезонеконвертер](https://github.com/mj1856/TimeZoneConverter) для обработки многоплатформенных идентификаторов с часовым поясом.

1. Выполните следующие команды в интерфейсе командной строки, чтобы установить зависимости.

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Identity.Web.UI --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.18.0
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a>Проектирование приложения

В этом разделе вы создадите базовую структуру пользовательского интерфейса приложения.

### <a name="implement-alert-extension-methods"></a>Реализация методов расширения оповещений

В этом разделе вы создадите методы расширения для типа, `IActionResult` возвращаемого представлениями контроллера. Это расширение позволит передавать в представление временные сообщения об ошибках или успешных попыток.

> [!TIP]
> Для редактирования исходных файлов в этом руководстве можно использовать любой текстовый редактор. Тем не менее, [Visual Studio Code](https://code.visualstudio.com/) предоставляет дополнительные функции, такие как отладка и IntelliSense.

1. Создайте новый каталог в каталоге **графтуториал** с именем **Alerts**.

1. Создайте новый файл с именем **WithAlertResult.CS** в каталоге **./алертс** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. Создайте новый файл с именем **AlertExtensions.CS** в каталоге **./алертс** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a>Реализация методов расширения пользовательских данных

В этом разделе вы создадите методы расширения для объекта, `ClaimsPrincipal` созданного платформой удостоверений Майкрософт. Это позволит расширить существующую идентификацию пользователя с помощью данных из Microsoft Graph.

> [!NOTE]
> Этот код — это просто заполнитель, который будет выполнен в более позднее время.

1. Создайте новый каталог в каталоге **графтуториал** с именем **Graph**.

1. Создайте новый файл с именем **GraphClaimsPrincipalExtensions.CS** и добавьте следующий код.

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

### <a name="create-views"></a>Создание представлений

В этом разделе будет реализовано представление Razor для приложения.

1. Добавьте новый файл с именем **_LoginPartial. cshtml** в каталоге **./виевс/Шаред** и добавьте приведенный ниже код.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. Добавьте новый файл с именем **_AlertPartial. cshtml** в каталоге **./виевс/Шаред** и добавьте приведенный ниже код.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. Откройте файл **./виевс/шаред/_layout. cshtml** и замените все его содержимое приведенным ниже кодом, чтобы обновить глобальную структуру приложения.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. Откройте файл **./ввврут/КСС/Сите.КСС** и добавьте приведенный ниже код в конец файла.

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. Откройте файл **./виевс/Хоме/индекс.кштмл** и замените его содержимое на приведенный ниже код.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. Создайте новый каталог в каталоге **./ввврут** с именем **img**. Добавьте в этот каталог файл изображения с именем **no-profile-photo.png** . Это изображение будет использоваться в качестве фотографии пользователя, когда у пользователя нет фотографии в Microsoft Graph.

    > [!TIP]
    > Вы можете скачать изображение, используемое на этих снимках экрана, из [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).

1. Сохраните все изменения и перезапустите сервер ( `dotnet run` ). Теперь приложение должно выглядеть по-другому.

    ![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
