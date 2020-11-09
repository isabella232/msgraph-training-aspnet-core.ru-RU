---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822396"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="84115-101">В этом разделе мы добавим возможность создания событий в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="84115-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-model"></a><span data-ttu-id="84115-102">Создание модели</span><span class="sxs-lookup"><span data-stu-id="84115-102">Create model</span></span>

1. <span data-ttu-id="84115-103">Создайте новый файл с именем **NewEvent.CS** в каталоге **./Models** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="84115-103">Create a new file named **NewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a><span data-ttu-id="84115-104">Создание представления</span><span class="sxs-lookup"><span data-stu-id="84115-104">Create view</span></span>

1. <span data-ttu-id="84115-105">Создайте новый файл с именем **New. cshtml** в каталоге He **./виевс/календар** и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="84115-105">Create a new file named **New.cshtml** in he **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a><span data-ttu-id="84115-106">Добавление действий контроллера</span><span class="sxs-lookup"><span data-stu-id="84115-106">Add controller actions</span></span>

1. <span data-ttu-id="84115-107">Откройте **./контроллерс/календарконтроллер.КС** и добавьте в класс следующее действие, `CalendarController` чтобы отобразить форму нового события.</span><span class="sxs-lookup"><span data-stu-id="84115-107">Open **./Controllers/CalendarController.cs** and add the following action to the `CalendarController` class to render the new event form.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. <span data-ttu-id="84115-108">Добавьте в класс следующее действие, `CalendarController` чтобы получить новое событие из формы, когда пользователь нажимает кнопку **сохранить** и использовать Microsoft Graph для добавления события в календарь пользователя.</span><span class="sxs-lookup"><span data-stu-id="84115-108">Add the following action to the `CalendarController` class to receive the new event from the form when the user clicks **Save** and use Microsoft Graph to add the event to the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. <span data-ttu-id="84115-109">Запустите приложение и войдите в систему, а затем щелкните ссылку " **Календарь** ".</span><span class="sxs-lookup"><span data-stu-id="84115-109">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="84115-110">Нажмите кнопку **создать событие** , заполните форму и нажмите кнопку **сохранить**.</span><span class="sxs-lookup"><span data-stu-id="84115-110">Click the **New event** button, fill in the form, and click **Save**.</span></span>

    ![Снимок экрана с формой создания события](./images/create-event-01.png)
