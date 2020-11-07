---
ms.openlocfilehash: 17394dd6283464eabcbea1f60c48640412b55431
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822320"
---
<!-- markdownlint-disable MD002 MD041 -->

Nesta seção, você incorporará o Microsoft Graph no aplicativo. Para este aplicativo, você usará a [biblioteca de cliente do Microsoft Graph para .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) para fazer chamadas para o Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obter eventos de calendário do Outlook

Comece criando um novo controlador para os modos de exibição de calendário.

1. Adicione um novo arquivo chamado **CalendarController.cs** no diretório **./Controllers** e adicione o código a seguir.

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

1. Adicione as seguintes funções à `CalendarController` classe para obter o modo de exibição calendário do usuário.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    Considere o que o código `GetUserWeekCalendar` faz.

    - Ele usa o fuso horário do usuário para obter valores de data/hora de início e de término UTC da semana.
    - Ele consulta o modo de [exibição calendário](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) do usuário para obter todos os eventos que estão entre a data/hora de início e término. O uso de um modo de exibição de calendário em vez de [eventos de listagem](/graph/api/user-list-events?view=graph-rest-1.0) expande eventos recorrentes, retornando qualquer ocorrência que acontecer na janela de tempo especificada.
    - Ele usa o `Prefer: outlook.timezone` cabeçalho para obter os resultados de volta no fuso horário do usuário.
    - Ele usa `Select` para limitar os campos que retornam apenas àqueles usados pelo aplicativo.
    - Ele usa `OrderBy` para classificar os resultados cronologicamente.
    - Ele usa uma `PageIterator` [página para através da coleção Events](/graph/sdks/paging). Isso trata o caso em que o usuário tem mais eventos em seu calendário do que o tamanho de página solicitado.

1. Adicione a função a seguir à `CalendarController` classe para implementar um modo de exibição temporário dos dados retornados.

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

1. Inicie o aplicativo, entre e clique no link **calendário** na barra de navegação. Se tudo funcionar, você deverá ver um despejo JSON de eventos no calendário do usuário.

## <a name="display-the-results"></a>Exibir os resultados

Agora você pode adicionar um modo de exibição para exibir os resultados de forma mais amigável.

### <a name="create-view-models"></a>Criar modelos de exibição

1. Crie um novo arquivo chamado **CalendarViewEvent.cs** no diretório **./Models** e adicione o código a seguir.

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. Crie um novo arquivo chamado **DailyViewModel.cs** no diretório **./Models** e adicione o código a seguir.

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. Crie um novo arquivo chamado **CalendarViewModel.cs** no diretório **./Models** e adicione o código a seguir.

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a>Criar modos de exibição

1. Crie um novo diretório chamado **calendário** no diretório **./views** .

1. Crie um novo arquivo chamado **_DailyEventsPartial. cshtml** no diretório **./views/Calendar** e adicione o código a seguir.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. Crie um novo arquivo chamado **index. cshtml** no diretório **./views/Calendar** e adicione o código a seguir.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a>Atualizar controlador de calendário

1. Abra **./Controllers/CalendarController.cs** e substitua a `Index` função existente pelo seguinte.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. Inicie o aplicativo, entre e clique no link do **calendário** . O aplicativo agora deve renderizar uma tabela de eventos.

    ![Uma captura de tela da tabela de eventos](./images/add-msgraph-01.png)
