---
ms.openlocfilehash: 17394dd6283464eabcbea1f60c48640412b55431
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822503"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом разделе в приложение будет включен Microsoft Graph. Для этого приложения вы будете использовать [клиентскую библиотеку Microsoft Graph для .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

Начните с создания нового контроллера для представлений календаря.

1. Добавьте новый файл с именем **CalendarController.CS** в каталог **./контроллерс** и добавьте следующий код.

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using TimeZoneConverter;

    namespace GraphTutorial.Controllers
    {
        public class CalendarController : Controller
        {
            private readonly GraphServiceClient _graphClient;
            private readonly ILogger<HomeController> _logger;

            public CalendarController(
                GraphServiceClient graphClient,
                ILogger<HomeController> logger)
            {
                _graphClient = graphClient;
                _logger = logger;
            }
        }
    }
    ```

1. Добавьте указанные ниже функции в `CalendarController` класс, чтобы получить представление календаря пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    Рассмотрите, что `GetUserWeekCalendar` делает код.

    - Он использует часовой пояс пользователя для получения значений даты и времени начала и окончания в формате UTC для недели.
    - Он запрашивает [представление календаря](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) пользователя, чтобы получить все события, которые попадают в начало и конец даты и времени. Использование представления календаря вместо [перечня событий](/graph/api/user-list-events?view=graph-rest-1.0) расширяет повторяющиеся события, возвращая все вхождения, происходящие в заданном временном окне.
    - Он использует `Prefer: outlook.timezone` заголовок, чтобы получить результаты обратно на часовом поясе пользователя.
    - Он используется `Select` для ограничения числа полей, возвращаемых только теми, которые используются приложением.
    - Он используется `OrderBy` для сортировки результатов в хронологическом порядке.
    - Используется `PageIterator` для [страницы "to Page" через коллекцию Events](/graph/sdks/paging). Это обрабатывает ситуацию, в которой у пользователя больше событий в календаре, чем запрошенный размер страницы.

1. Добавьте указанную ниже функцию в `CalendarController` класс, чтобы реализовать временное представление возвращаемых данных.

    ```csharp
    // Minimum permission scope needed for this view
    [AuthorizeForScopes(Scopes = new[] { "Calendars.Read" })]
    public async Task<IActionResult> Index()
    {
        try
        {
            var userTimeZone = TZConvert.GetTimeZoneInfo(
                User.GetUserGraphTimeZone());
            var startOfWeek = CalendarController.GetUtcStartOfWeekInTimeZone(
                DateTime.Today, userTimeZone);

            var events = await GetUserWeekCalendar(startOfWeek);

            // Return a JSON dump of events
            return new ContentResult {
                Content = _graphClient.HttpProvider.Serializer.SerializeObject(events),
                ContentType = "application/json"
            };
        }
        catch (ServiceException ex)
        {
            if (ex.InnerException is MicrosoftIdentityWebChallengeUserException)
            {
                throw ex;
            }

            return new ContentResult {
                Content = $"Error getting calendar view: {ex.Message}",
                ContentType = "text/plain"
            };
        }
    }
    ```

1. Запустите приложение и войдите в систему, а затем щелкните ссылку **Календарь** на панели навигации. Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете добавить представление для отображения результатов более удобным для пользователя способом.

### <a name="create-view-models"></a>Создание моделей представления

1. Создайте новый файл с именем **CalendarViewEvent.CS** в каталоге **./Models** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. Создайте новый файл с именем **DailyViewModel.CS** в каталоге **./Models** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. Создайте новый файл с именем **CalendarViewModel.CS** в каталоге **./Models** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a>Создание представлений

1. Создайте новый каталог с именем **Calendar** в каталоге **./views** .

1. Создайте новый файл с именем **_DailyEventsPartial. cshtml** в каталоге **./виевс/календар** и добавьте следующий код.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. Создайте новый файл с именем **index. cshtml** в каталоге **./виевс/календар** и добавьте приведенный ниже код.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a>Обновление контроллера календаря

1. Откройте **/контроллерс/календарконтроллер.КС** и замените существующую `Index` функцию на приведенную ниже.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. Запустите приложение и войдите в систему, а затем щелкните ссылку " **Календарь** ". Теперь приложение должно отображать таблицу событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
