---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822265"
---
<!-- markdownlint-disable MD002 MD041 -->

Nesta seção, você adicionará a capacidade de criar eventos no calendário do usuário.

## <a name="create-model"></a>Criar modelo

1. Crie um novo arquivo chamado **NewEvent.cs** no diretório **./Models** e adicione o código a seguir.

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a>Criar modo de exibição

1. Crie um novo arquivo chamado **New. cshtml** no diretório **./views/Calendar** e adicione o código a seguir.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a>Adicionar ações do controlador

1. Abra **./Controllers/CalendarController.cs** e adicione a ação a seguir à `CalendarController` classe para renderizar o novo formulário de evento.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. Adicione a ação a seguir à `CalendarController` classe para receber o novo evento do formulário quando o usuário clicar em **salvar** e usar o Microsoft Graph para adicionar o evento ao calendário do usuário.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. Inicie o aplicativo, entre e clique no link do **calendário** . Clique no botão **novo evento** , preencha o formulário e clique em **salvar**.

    ![Uma captura de tela do novo formulário de evento](./images/create-event-01.png)
