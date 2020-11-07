---
ms.openlocfilehash: 308938efbedc4618c7b0ca3ea6b2eebc0582da10
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822290"
---
<!-- markdownlint-disable MD002 MD041 -->

Comece criando um aplicativo Web do ASP.NET Core.

1. Abra a interface de linha de comando (CLI) em um diretório onde você deseja criar o projeto. Execute o seguinte comando.

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. Depois que o projeto for criado, verifique se ele funciona alterando o diretório atual para o diretório **GraphTutorial** e executando o seguinte comando na sua CLI.

    ```Shell
    dotnet run
    ```

1. Abra o navegador e navegue até `https://localhost:5001` . Se tudo estiver funcionando, você deverá ver uma página padrão do ASP.NET Core.

> [!IMPORTANT]
> Se você receber um aviso de que o certificado para **localhost** é não confiável, você pode usar a CLI do .NET Core para instalar e confiar no certificado de desenvolvimento. Consulte [impor HTTPS no ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) para obter instruções para sistemas operacionais específicos.

## <a name="add-nuget-packages"></a>Adicionar pacotes NuGet

Antes de prosseguir, instale alguns pacotes NuGet adicionais que serão usados posteriormente.

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) para solicitar e gerenciar tokens de acesso.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) para adicionar o SDK do Microsoft Graph via injeção de dependência.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) para entrada e interface de usuário de saída.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) para fazer chamadas para o Microsoft Graph.
- [Timezoneconverter](https://github.com/mj1856/TimeZoneConverter) para lidar com identificadores de fuso horário entre plataformas.

1. Execute os seguintes comandos em sua CLI para instalar as dependências.

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Identity.Web.UI --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.18.0
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a>Projetar o aplicativo

Nesta seção, você criará a estrutura básica de interface do usuário do aplicativo.

### <a name="implement-alert-extension-methods"></a>Implementar métodos de extensão de alerta

Nesta seção, você criará métodos de extensão para o `IActionResult` tipo retornado por modos de exibição do controlador. Essa extensão permitirá a passagem de mensagens de erro temporários ou de êxito para o modo de exibição.

> [!TIP]
> Você pode usar qualquer editor de texto para editar os arquivos de origem deste tutorial. No entanto, o [código do Visual Studio](https://code.visualstudio.com/) fornece recursos adicionais, como depuração e IntelliSense.

1. Crie um novo diretório no diretório **GraphTutorial** chamado **alertas**.

1. Crie um novo arquivo chamado **WithAlertResult.cs** no diretório **./Alerts** e adicione o código a seguir.

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. Crie um novo arquivo chamado **AlertExtensions.cs** no diretório **./Alerts** e adicione o código a seguir.

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a>Implementar métodos de extensão de dados do usuário

Nesta seção, você criará métodos de extensão para o `ClaimsPrincipal` objeto gerado pela plataforma de identidade da Microsoft. Isso permitirá que você estenda a identidade do usuário existente com os dados do Microsoft Graph.

> [!NOTE]
> Este código é apenas um espaço reservado por enquanto, você irá concluí-lo em uma seção posterior.

1. Crie um novo diretório no diretório **GraphTutorial** chamado **Graph**.

1. Crie um novo arquivo denominado **GraphClaimsPrincipalExtensions.cs** e adicione o código a seguir.

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

### <a name="create-views"></a>Criar modos de exibição

Nesta seção, você implementará os modos de exibição do Razor para o aplicativo.

1. Adicione um novo arquivo chamado **_LoginPartial. cshtml** no diretório **./views/Shared** e adicione o código a seguir.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. Adicione um novo arquivo chamado **_AlertPartial. cshtml** no diretório **./views/Shared** e adicione o código a seguir.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. Abra o arquivo **./Views/Shared/_Layout. cshtml** e substitua todo o conteúdo pelo código a seguir para atualizar o layout global do aplicativo.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. Abra **./wwwroot/CSS/site.css** e adicione o código a seguir na parte inferior do arquivo.

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. Abra o arquivo **./views/home/index.cshtml** e substitua o conteúdo pelo seguinte.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. Crie um novo diretório no diretório **./wwwroot** chamado **img**. Adicione um arquivo de imagem de sua escolha nomeada **no-profile-photo.png** nesse diretório. Esta imagem será usada como foto do usuário quando o usuário não tiver nenhuma foto no Microsoft Graph.

    > [!TIP]
    > É possível baixar a imagem usada nessas capturas de tela do [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).

1. Salve todas as suas alterações e reinicie o servidor ( `dotnet run` ). Agora, o aplicativo deve ser muito diferente.

    ![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)
