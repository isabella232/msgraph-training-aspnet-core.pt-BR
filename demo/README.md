---
ms.openlocfilehash: 30398bafbf47aaa374a200dd1834c9f2003e967f
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822279"
---
# <a name="how-to-run-the-completed-project"></a>Como executar o projeto concluído

## <a name="prerequisites"></a>Pré-requisitos

Para executar o projeto concluído nessa pasta, você precisará do seguinte:

- O [SDK do .NET Core](https://dotnet.microsoft.com/download) instalado em sua máquina de desenvolvimento. ( **Observação:** este tutorial foi escrito com o .NET Core SDK versão 3.1.201. As etapas deste guia podem funcionar com outras versões, mas que não foram testadas.
- Uma conta pessoal da Microsoft com uma caixa de correio no Outlook.com ou uma conta corporativa ou de estudante da Microsoft.

Se você não tem uma conta da Microsoft, há algumas opções para obter uma conta gratuita:

- Você pode [se inscrever para uma nova conta pessoal da Microsoft](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Você pode [se inscrever no programa para desenvolvedores do office 365](https://developer.microsoft.com/office/dev-program) para obter uma assinatura gratuita do Office 365.

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Registrar um aplicativo Web com o centro de administração do Azure Active Directory

1. Abra um navegador e navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com). Faça logon usando uma **conta pessoal** (também conhecida como Conta da Microsoft) ou **Conta Corporativa ou de Estudante**.

1. Selecione **Azure Active Directory** na navegação esquerda e selecione **Registros de aplicativos** em **Gerenciar**.

    ![Uma captura de tela dos registros de aplicativo ](../tutorial/images/aad-portal-app-registrations.png)

1. Selecione **Novo registro**. Na página **Registrar um aplicativo** , defina os valores da seguinte forma.

    - Defina **Nome** para `ASP.NET Core Graph Tutorial`.
    - Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
    - Em **URI de Redirecionamento** , defina o primeiro menu suspenso para `Web` e defina o valor como `https://localhost:5001/`.

    ![Uma captura de tela da página registrar um aplicativo](../tutorial/images/aad-register-an-app.png)

1. Selecione **Registrar**. Na página de **tutorial do gráfico principal do ASP.net** , copie o valor da ID do **aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.

    ![Uma captura de tela da ID do aplicativo do novo registro de aplicativo](../tutorial/images/aad-application-id.png)

1. Selecione **Autenticação** em **Gerenciar**. Em **URIs de redirecionamento** , adicione um URI com o valor `https://localhost:5001/signin-oidc` .

1. Definir a **URL de logout** como `https://localhost:5001/signout-oidc` .

1. Localize a seção **Concessão Implícita** e habilite **tokens de ID**. Selecione **Salvar**.

    ![Uma captura de tela das configurações da plataforma Web no portal do Azure](../tutorial/images/aad-web-platform.png)

1. Selecione **Certificados e segredos** sob **Gerenciar**. Selecione o botão **Novo segredo do cliente**. Insira um valor em **Descrição** e selecione uma das opções para **expirar** e selecione **Adicionar**.

    ![Uma captura de tela da caixa de diálogo Adicionar um segredo do cliente](../tutorial/images/aad-new-client-secret.png)

1. Copie o valor secreto do cliente antes de sair desta página. Você precisará dele na próxima etapa.

    > [!IMPORTANT]
    > Este segredo do cliente nunca é mostrado novamente, portanto, copie-o agora.

    ![Uma captura de tela do novo segredo do cliente recentemente adicionado](../tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a>Configurar o exemplo

1. Abra a interface de linha de comando (CLI) no diretório onde o **GraphTutorial. csproj** está localizado e execute os seguintes comandos, substituindo `YOUR_APP_ID` pela ID do aplicativo do portal do Azure e `YOUR_APP_SECRET` com o segredo do aplicativo.

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Na sua CLI, execute o seguinte comando para iniciar o aplicativo.

```Shell
dotnet run
```
