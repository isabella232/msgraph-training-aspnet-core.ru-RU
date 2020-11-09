---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822396"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом разделе мы добавим возможность создания событий в календаре пользователя.

## <a name="create-model"></a>Создание модели

1. Создайте новый файл с именем **NewEvent.CS** в каталоге **./Models** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a>Создание представления

1. Создайте новый файл с именем **New. cshtml** в каталоге He **./виевс/календар** и добавьте приведенный ниже код.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a>Добавление действий контроллера

1. Откройте **./контроллерс/календарконтроллер.КС** и добавьте в класс следующее действие, `CalendarController` чтобы отобразить форму нового события.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. Добавьте в класс следующее действие, `CalendarController` чтобы получить новое событие из формы, когда пользователь нажимает кнопку **сохранить** и использовать Microsoft Graph для добавления события в календарь пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. Запустите приложение и войдите в систему, а затем щелкните ссылку " **Календарь** ". Нажмите кнопку **создать событие** , заполните форму и нажмите кнопку **сохранить**.

    ![Снимок экрана с формой создания события](./images/create-event-01.png)
