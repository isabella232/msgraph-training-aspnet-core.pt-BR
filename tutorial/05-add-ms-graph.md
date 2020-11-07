---
ms.openlocfilehash: 17394dd6283464eabcbea1f60c48640412b55431
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822320"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6c71d-101">Nesta seção, você incorporará o Microsoft Graph no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c71d-101">In this section you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="6c71d-102">Para este aplicativo, você usará a [biblioteca de cliente do Microsoft Graph para .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="6c71d-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="6c71d-103">Obter eventos de calendário do Outlook</span><span class="sxs-lookup"><span data-stu-id="6c71d-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="6c71d-104">Comece criando um novo controlador para os modos de exibição de calendário.</span><span class="sxs-lookup"><span data-stu-id="6c71d-104">Start by creating a new controller for calendar views.</span></span>

1. <span data-ttu-id="6c71d-105">Adicione um novo arquivo chamado **CalendarController.cs** no diretório **./Controllers** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="6c71d-105">Add a new file named **CalendarController.cs** in the **./Controllers** directory and add the following code.</span></span>

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

1. <span data-ttu-id="6c71d-106">Adicione as seguintes funções à `CalendarController` classe para obter o modo de exibição calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="6c71d-106">Add the following functions to the `CalendarController` class to get the user's calendar view.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    <span data-ttu-id="6c71d-107">Considere o que o código `GetUserWeekCalendar` faz.</span><span class="sxs-lookup"><span data-stu-id="6c71d-107">Consider what the code in `GetUserWeekCalendar` does.</span></span>

    - <span data-ttu-id="6c71d-108">Ele usa o fuso horário do usuário para obter valores de data/hora de início e de término UTC da semana.</span><span class="sxs-lookup"><span data-stu-id="6c71d-108">It uses the user's time zone to get UTC start and end date/time values for the week.</span></span>
    - <span data-ttu-id="6c71d-109">Ele consulta o modo de [exibição calendário](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) do usuário para obter todos os eventos que estão entre a data/hora de início e término.</span><span class="sxs-lookup"><span data-stu-id="6c71d-109">It queries the user's [calendar view](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) to get all events that fall between the start and end date/times.</span></span> <span data-ttu-id="6c71d-110">O uso de um modo de exibição de calendário em vez de [eventos de listagem](/graph/api/user-list-events?view=graph-rest-1.0) expande eventos recorrentes, retornando qualquer ocorrência que acontecer na janela de tempo especificada.</span><span class="sxs-lookup"><span data-stu-id="6c71d-110">Using a calendar view instead of [listing events](/graph/api/user-list-events?view=graph-rest-1.0) expands recurring events, returning any occurrences that happen in the specified time window.</span></span>
    - <span data-ttu-id="6c71d-111">Ele usa o `Prefer: outlook.timezone` cabeçalho para obter os resultados de volta no fuso horário do usuário.</span><span class="sxs-lookup"><span data-stu-id="6c71d-111">It uses the `Prefer: outlook.timezone` header to get results back in the user's timezone.</span></span>
    - <span data-ttu-id="6c71d-112">Ele usa `Select` para limitar os campos que retornam apenas àqueles usados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c71d-112">It uses `Select` to limit the fields that come back to just those used by the app.</span></span>
    - <span data-ttu-id="6c71d-113">Ele usa `OrderBy` para classificar os resultados cronologicamente.</span><span class="sxs-lookup"><span data-stu-id="6c71d-113">It uses `OrderBy` to sort the results chronologically.</span></span>
    - <span data-ttu-id="6c71d-114">Ele usa uma `PageIterator` [página para através da coleção Events](/graph/sdks/paging).</span><span class="sxs-lookup"><span data-stu-id="6c71d-114">It uses a `PageIterator` to [page through the events collection](/graph/sdks/paging).</span></span> <span data-ttu-id="6c71d-115">Isso trata o caso em que o usuário tem mais eventos em seu calendário do que o tamanho de página solicitado.</span><span class="sxs-lookup"><span data-stu-id="6c71d-115">This handles the case where the user has more events on their calendar than the requested page size.</span></span>

1. <span data-ttu-id="6c71d-116">Adicione a função a seguir à `CalendarController` classe para implementar um modo de exibição temporário dos dados retornados.</span><span class="sxs-lookup"><span data-stu-id="6c71d-116">Add the following function to the `CalendarController` class to implement a temporary view of the returned data.</span></span>

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

1. <span data-ttu-id="6c71d-117">Inicie o aplicativo, entre e clique no link **calendário** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="6c71d-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="6c71d-118">Se tudo funcionar, você deverá ver um despejo JSON de eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="6c71d-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="6c71d-119">Exibir os resultados</span><span class="sxs-lookup"><span data-stu-id="6c71d-119">Display the results</span></span>

<span data-ttu-id="6c71d-120">Agora você pode adicionar um modo de exibição para exibir os resultados de forma mais amigável.</span><span class="sxs-lookup"><span data-stu-id="6c71d-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

### <a name="create-view-models"></a><span data-ttu-id="6c71d-121">Criar modelos de exibição</span><span class="sxs-lookup"><span data-stu-id="6c71d-121">Create view models</span></span>

1. <span data-ttu-id="6c71d-122">Crie um novo arquivo chamado **CalendarViewEvent.cs** no diretório **./Models** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="6c71d-122">Create a new file named **CalendarViewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. <span data-ttu-id="6c71d-123">Crie um novo arquivo chamado **DailyViewModel.cs** no diretório **./Models** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="6c71d-123">Create a new file named **DailyViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. <span data-ttu-id="6c71d-124">Crie um novo arquivo chamado **CalendarViewModel.cs** no diretório **./Models** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="6c71d-124">Create a new file named **CalendarViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a><span data-ttu-id="6c71d-125">Criar modos de exibição</span><span class="sxs-lookup"><span data-stu-id="6c71d-125">Create views</span></span>

1. <span data-ttu-id="6c71d-126">Crie um novo diretório chamado **calendário** no diretório **./views** .</span><span class="sxs-lookup"><span data-stu-id="6c71d-126">Create a new directory named **Calendar** in the **./Views** directory.</span></span>

1. <span data-ttu-id="6c71d-127">Crie um novo arquivo chamado **_DailyEventsPartial. cshtml** no diretório **./views/Calendar** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="6c71d-127">Create a new file named **_DailyEventsPartial.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. <span data-ttu-id="6c71d-128">Crie um novo arquivo chamado **index. cshtml** no diretório **./views/Calendar** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="6c71d-128">Create a new file named **Index.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a><span data-ttu-id="6c71d-129">Atualizar controlador de calendário</span><span class="sxs-lookup"><span data-stu-id="6c71d-129">Update calendar controller</span></span>

1. <span data-ttu-id="6c71d-130">Abra **./Controllers/CalendarController.cs** e substitua a `Index` função existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6c71d-130">Open **./Controllers/CalendarController.cs** and replace the existing `Index` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. <span data-ttu-id="6c71d-131">Inicie o aplicativo, entre e clique no link do **calendário** .</span><span class="sxs-lookup"><span data-stu-id="6c71d-131">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="6c71d-132">O aplicativo agora deve renderizar uma tabela de eventos.</span><span class="sxs-lookup"><span data-stu-id="6c71d-132">The app should now render a table of events.</span></span>

    ![Uma captura de tela da tabela de eventos](./images/add-msgraph-01.png)
