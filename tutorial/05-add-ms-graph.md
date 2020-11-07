---
ms.openlocfilehash: 17394dd6283464eabcbea1f60c48640412b55431
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822503"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="22b23-101">В этом разделе в приложение будет включен Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="22b23-101">In this section you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="22b23-102">Для этого приложения вы будете использовать [клиентскую библиотеку Microsoft Graph для .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="22b23-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="22b23-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="22b23-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="22b23-104">Начните с создания нового контроллера для представлений календаря.</span><span class="sxs-lookup"><span data-stu-id="22b23-104">Start by creating a new controller for calendar views.</span></span>

1. <span data-ttu-id="22b23-105">Добавьте новый файл с именем **CalendarController.CS** в каталог **./контроллерс** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="22b23-105">Add a new file named **CalendarController.cs** in the **./Controllers** directory and add the following code.</span></span>

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

1. <span data-ttu-id="22b23-106">Добавьте указанные ниже функции в `CalendarController` класс, чтобы получить представление календаря пользователя.</span><span class="sxs-lookup"><span data-stu-id="22b23-106">Add the following functions to the `CalendarController` class to get the user's calendar view.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    <span data-ttu-id="22b23-107">Рассмотрите, что `GetUserWeekCalendar` делает код.</span><span class="sxs-lookup"><span data-stu-id="22b23-107">Consider what the code in `GetUserWeekCalendar` does.</span></span>

    - <span data-ttu-id="22b23-108">Он использует часовой пояс пользователя для получения значений даты и времени начала и окончания в формате UTC для недели.</span><span class="sxs-lookup"><span data-stu-id="22b23-108">It uses the user's time zone to get UTC start and end date/time values for the week.</span></span>
    - <span data-ttu-id="22b23-109">Он запрашивает [представление календаря](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) пользователя, чтобы получить все события, которые попадают в начало и конец даты и времени.</span><span class="sxs-lookup"><span data-stu-id="22b23-109">It queries the user's [calendar view](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) to get all events that fall between the start and end date/times.</span></span> <span data-ttu-id="22b23-110">Использование представления календаря вместо [перечня событий](/graph/api/user-list-events?view=graph-rest-1.0) расширяет повторяющиеся события, возвращая все вхождения, происходящие в заданном временном окне.</span><span class="sxs-lookup"><span data-stu-id="22b23-110">Using a calendar view instead of [listing events](/graph/api/user-list-events?view=graph-rest-1.0) expands recurring events, returning any occurrences that happen in the specified time window.</span></span>
    - <span data-ttu-id="22b23-111">Он использует `Prefer: outlook.timezone` заголовок, чтобы получить результаты обратно на часовом поясе пользователя.</span><span class="sxs-lookup"><span data-stu-id="22b23-111">It uses the `Prefer: outlook.timezone` header to get results back in the user's timezone.</span></span>
    - <span data-ttu-id="22b23-112">Он используется `Select` для ограничения числа полей, возвращаемых только теми, которые используются приложением.</span><span class="sxs-lookup"><span data-stu-id="22b23-112">It uses `Select` to limit the fields that come back to just those used by the app.</span></span>
    - <span data-ttu-id="22b23-113">Он используется `OrderBy` для сортировки результатов в хронологическом порядке.</span><span class="sxs-lookup"><span data-stu-id="22b23-113">It uses `OrderBy` to sort the results chronologically.</span></span>
    - <span data-ttu-id="22b23-114">Используется `PageIterator` для [страницы "to Page" через коллекцию Events](/graph/sdks/paging).</span><span class="sxs-lookup"><span data-stu-id="22b23-114">It uses a `PageIterator` to [page through the events collection](/graph/sdks/paging).</span></span> <span data-ttu-id="22b23-115">Это обрабатывает ситуацию, в которой у пользователя больше событий в календаре, чем запрошенный размер страницы.</span><span class="sxs-lookup"><span data-stu-id="22b23-115">This handles the case where the user has more events on their calendar than the requested page size.</span></span>

1. <span data-ttu-id="22b23-116">Добавьте указанную ниже функцию в `CalendarController` класс, чтобы реализовать временное представление возвращаемых данных.</span><span class="sxs-lookup"><span data-stu-id="22b23-116">Add the following function to the `CalendarController` class to implement a temporary view of the returned data.</span></span>

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

1. <span data-ttu-id="22b23-117">Запустите приложение и войдите в систему, а затем щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="22b23-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="22b23-118">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="22b23-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="22b23-119">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="22b23-119">Display the results</span></span>

<span data-ttu-id="22b23-120">Теперь вы можете добавить представление для отображения результатов более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="22b23-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

### <a name="create-view-models"></a><span data-ttu-id="22b23-121">Создание моделей представления</span><span class="sxs-lookup"><span data-stu-id="22b23-121">Create view models</span></span>

1. <span data-ttu-id="22b23-122">Создайте новый файл с именем **CalendarViewEvent.CS** в каталоге **./Models** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="22b23-122">Create a new file named **CalendarViewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. <span data-ttu-id="22b23-123">Создайте новый файл с именем **DailyViewModel.CS** в каталоге **./Models** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="22b23-123">Create a new file named **DailyViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. <span data-ttu-id="22b23-124">Создайте новый файл с именем **CalendarViewModel.CS** в каталоге **./Models** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="22b23-124">Create a new file named **CalendarViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a><span data-ttu-id="22b23-125">Создание представлений</span><span class="sxs-lookup"><span data-stu-id="22b23-125">Create views</span></span>

1. <span data-ttu-id="22b23-126">Создайте новый каталог с именем **Calendar** в каталоге **./views** .</span><span class="sxs-lookup"><span data-stu-id="22b23-126">Create a new directory named **Calendar** in the **./Views** directory.</span></span>

1. <span data-ttu-id="22b23-127">Создайте новый файл с именем **_DailyEventsPartial. cshtml** в каталоге **./виевс/календар** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="22b23-127">Create a new file named **_DailyEventsPartial.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. <span data-ttu-id="22b23-128">Создайте новый файл с именем **index. cshtml** в каталоге **./виевс/календар** и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="22b23-128">Create a new file named **Index.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a><span data-ttu-id="22b23-129">Обновление контроллера календаря</span><span class="sxs-lookup"><span data-stu-id="22b23-129">Update calendar controller</span></span>

1. <span data-ttu-id="22b23-130">Откройте **/контроллерс/календарконтроллер.КС** и замените существующую `Index` функцию на приведенную ниже.</span><span class="sxs-lookup"><span data-stu-id="22b23-130">Open **./Controllers/CalendarController.cs** and replace the existing `Index` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. <span data-ttu-id="22b23-131">Запустите приложение и войдите в систему, а затем щелкните ссылку " **Календарь** ".</span><span class="sxs-lookup"><span data-stu-id="22b23-131">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="22b23-132">Теперь приложение должно отображать таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="22b23-132">The app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
