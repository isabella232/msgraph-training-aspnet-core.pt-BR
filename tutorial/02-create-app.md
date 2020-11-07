---
ms.openlocfilehash: 308938efbedc4618c7b0ca3ea6b2eebc0582da10
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822290"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0c537-101">Comece criando um aplicativo Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c537-101">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="0c537-102">Abra a interface de linha de comando (CLI) em um diretório onde você deseja criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="0c537-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="0c537-103">Execute o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="0c537-103">Run the following command.</span></span>

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. <span data-ttu-id="0c537-104">Depois que o projeto for criado, verifique se ele funciona alterando o diretório atual para o diretório **GraphTutorial** e executando o seguinte comando na sua CLI.</span><span class="sxs-lookup"><span data-stu-id="0c537-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="0c537-105">Abra o navegador e navegue até `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="0c537-105">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="0c537-106">Se tudo estiver funcionando, você deverá ver uma página padrão do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c537-106">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0c537-107">Se você receber um aviso de que o certificado para **localhost** é não confiável, você pode usar a CLI do .NET Core para instalar e confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0c537-107">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="0c537-108">Consulte [impor HTTPS no ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) para obter instruções para sistemas operacionais específicos.</span><span class="sxs-lookup"><span data-stu-id="0c537-108">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="0c537-109">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="0c537-109">Add NuGet packages</span></span>

<span data-ttu-id="0c537-110">Antes de prosseguir, instale alguns pacotes NuGet adicionais que serão usados posteriormente.</span><span class="sxs-lookup"><span data-stu-id="0c537-110">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="0c537-111">[Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) para solicitar e gerenciar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="0c537-111">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="0c537-112">[Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) para adicionar o SDK do Microsoft Graph via injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0c537-112">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding the Microsoft Graph SDK via dependency injection.</span></span>
- <span data-ttu-id="0c537-113">[Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) para entrada e interface de usuário de saída.</span><span class="sxs-lookup"><span data-stu-id="0c537-113">[Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) for sign-in and sign-out UI.</span></span>
- <span data-ttu-id="0c537-114">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0c537-114">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="0c537-115">[Timezoneconverter](https://github.com/mj1856/TimeZoneConverter) para lidar com identificadores de fuso horário entre plataformas.</span><span class="sxs-lookup"><span data-stu-id="0c537-115">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for handling time zoned identifiers cross-platform.</span></span>

1. <span data-ttu-id="0c537-116">Execute os seguintes comandos em sua CLI para instalar as dependências.</span><span class="sxs-lookup"><span data-stu-id="0c537-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Identity.Web.UI --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.18.0
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="0c537-117">Projetar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0c537-117">Design the app</span></span>

<span data-ttu-id="0c537-118">Nesta seção, você criará a estrutura básica de interface do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c537-118">In this section you will create the basic UI structure of the application.</span></span>

### <a name="implement-alert-extension-methods"></a><span data-ttu-id="0c537-119">Implementar métodos de extensão de alerta</span><span class="sxs-lookup"><span data-stu-id="0c537-119">Implement alert extension methods</span></span>

<span data-ttu-id="0c537-120">Nesta seção, você criará métodos de extensão para o `IActionResult` tipo retornado por modos de exibição do controlador.</span><span class="sxs-lookup"><span data-stu-id="0c537-120">In this section you will create extension methods for the `IActionResult` type returned by controller views.</span></span> <span data-ttu-id="0c537-121">Essa extensão permitirá a passagem de mensagens de erro temporários ou de êxito para o modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="0c537-121">This extension will enable passing temporary error or success messages to the view.</span></span>

> [!TIP]
> <span data-ttu-id="0c537-122">Você pode usar qualquer editor de texto para editar os arquivos de origem deste tutorial.</span><span class="sxs-lookup"><span data-stu-id="0c537-122">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="0c537-123">No entanto, o [código do Visual Studio](https://code.visualstudio.com/) fornece recursos adicionais, como depuração e IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="0c537-123">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="0c537-124">Crie um novo diretório no diretório **GraphTutorial** chamado **alertas**.</span><span class="sxs-lookup"><span data-stu-id="0c537-124">Create a new directory in the **GraphTutorial** directory named **Alerts**.</span></span>

1. <span data-ttu-id="0c537-125">Crie um novo arquivo chamado **WithAlertResult.cs** no diretório **./Alerts** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0c537-125">Create a new file named **WithAlertResult.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. <span data-ttu-id="0c537-126">Crie um novo arquivo chamado **AlertExtensions.cs** no diretório **./Alerts** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0c537-126">Create a new file named **AlertExtensions.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a><span data-ttu-id="0c537-127">Implementar métodos de extensão de dados do usuário</span><span class="sxs-lookup"><span data-stu-id="0c537-127">Implement user data extension methods</span></span>

<span data-ttu-id="0c537-128">Nesta seção, você criará métodos de extensão para o `ClaimsPrincipal` objeto gerado pela plataforma de identidade da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0c537-128">In this section you will create extension methods for the `ClaimsPrincipal` object generated by the Microsoft Identity platform.</span></span> <span data-ttu-id="0c537-129">Isso permitirá que você estenda a identidade do usuário existente com os dados do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0c537-129">This will allow you to extend the existing user identity with data from Microsoft Graph.</span></span>

> [!NOTE]
> <span data-ttu-id="0c537-130">Este código é apenas um espaço reservado por enquanto, você irá concluí-lo em uma seção posterior.</span><span class="sxs-lookup"><span data-stu-id="0c537-130">This code is just a placeholder for now, you will complete it in a later section.</span></span>

1. <span data-ttu-id="0c537-131">Crie um novo diretório no diretório **GraphTutorial** chamado **Graph**.</span><span class="sxs-lookup"><span data-stu-id="0c537-131">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>

1. <span data-ttu-id="0c537-132">Crie um novo arquivo denominado **GraphClaimsPrincipalExtensions.cs** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0c537-132">Create a new file named **GraphClaimsPrincipalExtensions.cs** and add the following code.</span></span>

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

### <a name="create-views"></a><span data-ttu-id="0c537-133">Criar modos de exibição</span><span class="sxs-lookup"><span data-stu-id="0c537-133">Create views</span></span>

<span data-ttu-id="0c537-134">Nesta seção, você implementará os modos de exibição do Razor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c537-134">In this section you will implement the Razor views for the application.</span></span>

1. <span data-ttu-id="0c537-135">Adicione um novo arquivo chamado **_LoginPartial. cshtml** no diretório **./views/Shared** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0c537-135">Add a new file named **_LoginPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. <span data-ttu-id="0c537-136">Adicione um novo arquivo chamado **_AlertPartial. cshtml** no diretório **./views/Shared** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0c537-136">Add a new file named **_AlertPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. <span data-ttu-id="0c537-137">Abra o arquivo **./Views/Shared/_Layout. cshtml** e substitua todo o conteúdo pelo código a seguir para atualizar o layout global do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c537-137">Open the **./Views/Shared/_Layout.cshtml** file, and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. <span data-ttu-id="0c537-138">Abra **./wwwroot/CSS/site.css** e adicione o código a seguir na parte inferior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0c537-138">Open **./wwwroot/css/site.css** and add the following code at the bottom of the file.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="0c537-139">Abra o arquivo **./views/home/index.cshtml** e substitua o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="0c537-139">Open the **./Views/Home/index.cshtml** file and replace its contents with the following.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. <span data-ttu-id="0c537-140">Crie um novo diretório no diretório **./wwwroot** chamado **img**.</span><span class="sxs-lookup"><span data-stu-id="0c537-140">Create a new directory in the **./wwwroot** directory named **img**.</span></span> <span data-ttu-id="0c537-141">Adicione um arquivo de imagem de sua escolha nomeada **no-profile-photo.png** nesse diretório.</span><span class="sxs-lookup"><span data-stu-id="0c537-141">Add an image file of your choosing named **no-profile-photo.png** in this directory.</span></span> <span data-ttu-id="0c537-142">Esta imagem será usada como foto do usuário quando o usuário não tiver nenhuma foto no Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0c537-142">This image will be used as the user's photo when the user has no photo in Microsoft Graph.</span></span>

    > [!TIP]
    > <span data-ttu-id="0c537-143">É possível baixar a imagem usada nessas capturas de tela do [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).</span><span class="sxs-lookup"><span data-stu-id="0c537-143">You can download the image used in these screenshots from [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).</span></span>

1. <span data-ttu-id="0c537-144">Salve todas as suas alterações e reinicie o servidor ( `dotnet run` ).</span><span class="sxs-lookup"><span data-stu-id="0c537-144">Save all of your changes and restart the server (`dotnet run`).</span></span> <span data-ttu-id="0c537-145">Agora, o aplicativo deve ser muito diferente.</span><span class="sxs-lookup"><span data-stu-id="0c537-145">Now, the app should look very different.</span></span>

    ![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)
