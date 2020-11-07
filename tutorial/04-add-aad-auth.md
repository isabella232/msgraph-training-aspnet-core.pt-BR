---
ms.openlocfilehash: 6e6c476b4ff0901f50d8e35a17f584d73b48b533
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822287"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c39aa-101">Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="c39aa-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="c39aa-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar a API do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c39aa-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="c39aa-103">Nesta etapa, você irá configurar a biblioteca [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) .</span><span class="sxs-lookup"><span data-stu-id="c39aa-103">In this step you will configure the [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) library.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c39aa-104">Para evitar o armazenamento da ID do aplicativo e o segredo na fonte, você usará o [Gerenciador de segredo do .net](/aspnet/core/security/app-secrets) para armazenar esses valores.</span><span class="sxs-lookup"><span data-stu-id="c39aa-104">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="c39aa-105">O gerente secreto é apenas para fins de desenvolvimento, os aplicativos de produção devem usar um Gerenciador de segredo confiável para armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="c39aa-105">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

1. <span data-ttu-id="c39aa-106">Abra **./appsettings.jsem** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c39aa-106">Open **./appsettings.json** and replace its contents with the following.</span></span>

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. <span data-ttu-id="c39aa-107">Abra a CLI no diretório onde o **GraphTutorial. csproj** está localizado e execute os seguintes comandos, substituindo `YOUR_APP_ID` pela ID do aplicativo do portal do Azure e `YOUR_APP_SECRET` com o segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c39aa-107">Open your CLI in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="c39aa-108">Implementar logon</span><span class="sxs-lookup"><span data-stu-id="c39aa-108">Implement sign-in</span></span>

<span data-ttu-id="c39aa-109">Comece adicionando os serviços da plataforma de identidade da Microsoft ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c39aa-109">Start by adding the Microsoft Identity platform services to the application.</span></span>

1. <span data-ttu-id="c39aa-110">Crie um novo arquivo chamado **GraphConstants.cs** no diretório **./Graph** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="c39aa-110">Create a new file named **GraphConstants.cs** in the **./Graph** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. <span data-ttu-id="c39aa-111">Abra o arquivo **./Startup.cs** e adicione as seguintes `using` instruções à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c39aa-111">Open the **./Startup.cs** file and add the following `using` statements to the top of the file.</span></span>

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

1. <span data-ttu-id="c39aa-112">Substitua a função `ConfigureServices` existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c39aa-112">Replace the existing `ConfigureServices` function with the following.</span></span>

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

1. <span data-ttu-id="c39aa-113">Na `Configure` função, adicione a linha a seguir acima da `app.UseAuthorization();` linha.</span><span class="sxs-lookup"><span data-stu-id="c39aa-113">In the `Configure` function, add the following line above the `app.UseAuthorization();` line.</span></span>

    ```csharp
    app.UseAuthentication();
    ```

1. <span data-ttu-id="c39aa-114">Abra **./Controllers/HomeController.cs** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c39aa-114">Open **./Controllers/HomeController.cs** and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="c39aa-115">Salve suas alterações e inicie o projeto.</span><span class="sxs-lookup"><span data-stu-id="c39aa-115">Save your changes and start the project.</span></span> <span data-ttu-id="c39aa-116">Faça logon com sua conta da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c39aa-116">Login with your Microsoft account.</span></span>

1. <span data-ttu-id="c39aa-117">Examine o prompt de consentimento.</span><span class="sxs-lookup"><span data-stu-id="c39aa-117">Examine the consent prompt.</span></span> <span data-ttu-id="c39aa-118">A lista de permissões corresponde à lista de escopos de permissões configurados em **./Graph/GraphConstants.cs**.</span><span class="sxs-lookup"><span data-stu-id="c39aa-118">The list of permissions correspond to list of permissions scopes configured in **./Graph/GraphConstants.cs**.</span></span>

    - <span data-ttu-id="c39aa-119">**Manter acesso aos dados para os quais você concedeu acesso a:** ( `offline_access` ) essa permissão é solicitada pelo MSAL para recuperar Tokens de atualização.</span><span class="sxs-lookup"><span data-stu-id="c39aa-119">**Maintain access to data you have given it access to:** (`offline_access`) This permission is requested by MSAL in order to retrieve refresh tokens.</span></span>
    - <span data-ttu-id="c39aa-120">**Entre e leia seu perfil:** ( `User.Read` ) essa permissão permite que o aplicativo obtenha o perfil do usuário conectado e a foto do perfil.</span><span class="sxs-lookup"><span data-stu-id="c39aa-120">**Sign you in and read your profile:** (`User.Read`) This permission allows the app to get the logged-in user's profile and profile photo.</span></span>
    - <span data-ttu-id="c39aa-121">**Leia suas configurações de caixa de correio:** ( `MailboxSettings.Read` ) essa permissão permite que o aplicativo Leia as configurações da caixa de correio do usuário, incluindo o fuso horário e o formato de hora.</span><span class="sxs-lookup"><span data-stu-id="c39aa-121">**Read your mailbox settings:** (`MailboxSettings.Read`) This permission allows the app to read the user's mailbox settings, including time zone and time format.</span></span>
    - <span data-ttu-id="c39aa-122">**Ter acesso total aos seus calendários:** ( `Calendars.ReadWrite` ) essa permissão permite que o aplicativo Leia eventos no calendário do usuário, adicione novos eventos e modifique os existentes.</span><span class="sxs-lookup"><span data-stu-id="c39aa-122">**Have full access to your calendars:** (`Calendars.ReadWrite`) This permission allows the app to read events on the user's calendar, add new events, and modify existing ones.</span></span>

    ![Uma captura de tela do prompt de consentimento da plataforma de identidade da Microsoft](./images/add-aad-auth-03.png)

    <span data-ttu-id="c39aa-124">Para obter mais informações sobre o consentimento, consulte [Understanding Azure ad Application consente experiências](/azure/active-directory/develop/application-consent-experience).</span><span class="sxs-lookup"><span data-stu-id="c39aa-124">For more information regarding consent, see [Understanding Azure AD application consent experiences](/azure/active-directory/develop/application-consent-experience).</span></span>

1. <span data-ttu-id="c39aa-125">Consentimento para as permissões solicitadas.</span><span class="sxs-lookup"><span data-stu-id="c39aa-125">Consent to the requested permissions.</span></span> <span data-ttu-id="c39aa-126">O navegador redireciona para o aplicativo, mostrando o token.</span><span class="sxs-lookup"><span data-stu-id="c39aa-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="c39aa-127">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="c39aa-127">Get user details</span></span>

<span data-ttu-id="c39aa-128">Depois que o usuário estiver conectado, você pode obter as informações do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c39aa-128">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="c39aa-129">Abra **./Graph/GraphClaimsPrincipalExtensions.cs** e substitua todo o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c39aa-129">Open **./Graph/GraphClaimsPrincipalExtensions.cs** and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. <span data-ttu-id="c39aa-130">Abra **./Startup.cs** e substitua a `.AddMicrosoftIdentityWebApp(Configuration)` linha existente pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="c39aa-130">Open **./Startup.cs** and replace the existing `.AddMicrosoftIdentityWebApp(Configuration)` line with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    <span data-ttu-id="c39aa-131">Considere o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="c39aa-131">Consider what this code does.</span></span>

    - <span data-ttu-id="c39aa-132">Ele adiciona um manipulador de eventos para o `OnTokenValidated` evento.</span><span class="sxs-lookup"><span data-stu-id="c39aa-132">It adds an event handler for the `OnTokenValidated` event.</span></span>
        - <span data-ttu-id="c39aa-133">Ele usa a `ITokenAcquisition` interface para obter um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="c39aa-133">It uses the `ITokenAcquisition` interface to get an access token.</span></span>
        - <span data-ttu-id="c39aa-134">Ele chama o Microsoft Graph para obter o perfil e a foto do usuário.</span><span class="sxs-lookup"><span data-stu-id="c39aa-134">It calls Microsoft Graph to get the user's profile and photo.</span></span>
        - <span data-ttu-id="c39aa-135">Ele adiciona as informações do gráfico à identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="c39aa-135">It adds the Graph information to the user's identity.</span></span>

1. <span data-ttu-id="c39aa-136">Adicione a seguinte chamada de função após a `EnableTokenAcquisitionToCallDownstreamApi` chamada e antes da `AddInMemoryTokenCaches` chamada.</span><span class="sxs-lookup"><span data-stu-id="c39aa-136">Add the following function call after the `EnableTokenAcquisitionToCallDownstreamApi` call and before the `AddInMemoryTokenCaches` call.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    <span data-ttu-id="c39aa-137">Isso fará com que um **GraphServiceClient** autenticado esteja disponível para os controladores via injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="c39aa-137">This will make an authenticated **GraphServiceClient** available to controllers via dependency injection.</span></span>

1. <span data-ttu-id="c39aa-138">Abra **./Controllers/HomeController.cs** e substitua a `Index` função com o seguinte.</span><span class="sxs-lookup"><span data-stu-id="c39aa-138">Open **./Controllers/HomeController.cs** and replace the `Index` function with the following.</span></span>

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. <span data-ttu-id="c39aa-139">Remova todas as referências para `ITokenAcquisition` na classe **HomeController** .</span><span class="sxs-lookup"><span data-stu-id="c39aa-139">Remove all references to `ITokenAcquisition` in the **HomeController** class.</span></span>

1. <span data-ttu-id="c39aa-140">Salve suas alterações, inicie o aplicativo e execute o processo de entrada.</span><span class="sxs-lookup"><span data-stu-id="c39aa-140">Save your changes, start the app, and go through the sign-in process.</span></span> <span data-ttu-id="c39aa-141">Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.</span><span class="sxs-lookup"><span data-stu-id="c39aa-141">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

1. <span data-ttu-id="c39aa-143">Clique no avatar do usuário no canto superior direito para **acessar o link sair.**</span><span class="sxs-lookup"><span data-stu-id="c39aa-143">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="c39aa-144">Clicar **em sair** redefine a sessão e retorna à Home Page.</span><span class="sxs-lookup"><span data-stu-id="c39aa-144">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="c39aa-146">Armazenar e atualizar tokens</span><span class="sxs-lookup"><span data-stu-id="c39aa-146">Storing and refreshing tokens</span></span>

<span data-ttu-id="c39aa-147">Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="c39aa-147">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="c39aa-148">Este é o token que permite ao aplicativo acessar o Microsoft Graph em nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="c39aa-148">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="c39aa-149">No entanto, esse token é de vida curta.</span><span class="sxs-lookup"><span data-stu-id="c39aa-149">However, this token is short-lived.</span></span> <span data-ttu-id="c39aa-150">O token expira uma hora após sua emissão.</span><span class="sxs-lookup"><span data-stu-id="c39aa-150">The token expires an hour after it is issued.</span></span> <span data-ttu-id="c39aa-151">É onde o token de atualização se torna útil.</span><span class="sxs-lookup"><span data-stu-id="c39aa-151">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="c39aa-152">O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.</span><span class="sxs-lookup"><span data-stu-id="c39aa-152">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="c39aa-153">Como o aplicativo está usando a biblioteca Microsoft. Identity. Web, você não precisa implementar qualquer lógica de armazenamento ou de atualização.</span><span class="sxs-lookup"><span data-stu-id="c39aa-153">Because the app is using the Microsoft.Identity.Web library, you do not have to implement any token storage or refresh logic.</span></span>

<span data-ttu-id="c39aa-154">O aplicativo usa o cache de token na memória, o que é suficiente para aplicativos que não precisam persistir tokens quando o aplicativo é reiniciado.</span><span class="sxs-lookup"><span data-stu-id="c39aa-154">The app uses the in-memory token cache, which is sufficient for apps that do not need to persist tokens when the app restarts.</span></span> <span data-ttu-id="c39aa-155">Em vez disso, os aplicativos de produção podem usar as [Opções de cache distribuído](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) na biblioteca Microsoft. Identity. Web.</span><span class="sxs-lookup"><span data-stu-id="c39aa-155">Production apps may instead use the [distributed cache options](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in the Microsoft.Identity.Web library.</span></span>

<span data-ttu-id="c39aa-156">O `GetAccessTokenForUserAsync` método manipula a expiração do token e a atualização para você.</span><span class="sxs-lookup"><span data-stu-id="c39aa-156">The `GetAccessTokenForUserAsync` method handles token expiration and refresh for you.</span></span> <span data-ttu-id="c39aa-157">Primeiro ele verifica o token em cache e, se ele não tiver expirado, ele o retornará.</span><span class="sxs-lookup"><span data-stu-id="c39aa-157">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="c39aa-158">Se ele tiver expirado, ele usará o token de atualização em cache para obter um novo.</span><span class="sxs-lookup"><span data-stu-id="c39aa-158">If it is expired, it uses the cached refresh token to obtain a new one.</span></span>

<span data-ttu-id="c39aa-159">O **GraphServiceClient** que os controladores recebem por meio da injeção de dependência será pré-configurado com um provedor de autenticação que usa `GetAccessTokenForUserAsync` para você.</span><span class="sxs-lookup"><span data-stu-id="c39aa-159">The **GraphServiceClient** that controllers get via dependency injection will be pre-configured with an authentication provider that uses `GetAccessTokenForUserAsync` for you.</span></span>
