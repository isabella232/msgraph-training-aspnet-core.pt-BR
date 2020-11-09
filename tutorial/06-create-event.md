---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822265"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4bd13-101">Nesta seção, você adicionará a capacidade de criar eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="4bd13-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-model"></a><span data-ttu-id="4bd13-102">Criar modelo</span><span class="sxs-lookup"><span data-stu-id="4bd13-102">Create model</span></span>

1. <span data-ttu-id="4bd13-103">Crie um novo arquivo chamado **NewEvent.cs** no diretório **./Models** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="4bd13-103">Create a new file named **NewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a><span data-ttu-id="4bd13-104">Criar modo de exibição</span><span class="sxs-lookup"><span data-stu-id="4bd13-104">Create view</span></span>

1. <span data-ttu-id="4bd13-105">Crie um novo arquivo chamado **New. cshtml** no diretório **./views/Calendar** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="4bd13-105">Create a new file named **New.cshtml** in he **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a><span data-ttu-id="4bd13-106">Adicionar ações do controlador</span><span class="sxs-lookup"><span data-stu-id="4bd13-106">Add controller actions</span></span>

1. <span data-ttu-id="4bd13-107">Abra **./Controllers/CalendarController.cs** e adicione a ação a seguir à `CalendarController` classe para renderizar o novo formulário de evento.</span><span class="sxs-lookup"><span data-stu-id="4bd13-107">Open **./Controllers/CalendarController.cs** and add the following action to the `CalendarController` class to render the new event form.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. <span data-ttu-id="4bd13-108">Adicione a ação a seguir à `CalendarController` classe para receber o novo evento do formulário quando o usuário clicar em **salvar** e usar o Microsoft Graph para adicionar o evento ao calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="4bd13-108">Add the following action to the `CalendarController` class to receive the new event from the form when the user clicks **Save** and use Microsoft Graph to add the event to the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. <span data-ttu-id="4bd13-109">Inicie o aplicativo, entre e clique no link do **calendário** .</span><span class="sxs-lookup"><span data-stu-id="4bd13-109">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="4bd13-110">Clique no botão **novo evento** , preencha o formulário e clique em **salvar**.</span><span class="sxs-lookup"><span data-stu-id="4bd13-110">Click the **New event** button, fill in the form, and click **Save**.</span></span>

    ![Uma captura de tela do novo formulário de evento](./images/create-event-01.png)
