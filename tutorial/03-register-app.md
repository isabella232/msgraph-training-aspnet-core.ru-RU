---
ms.openlocfilehash: debd685996df22a83110a14ca585cfafa4e08a0d
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822431"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a08b3-101">В этом упражнении вы создадите регистрацию нового веб-приложения Azure AD с помощью центра администрирования Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a08b3-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="a08b3-102">Откройте браузер и перейдите к [Центру администрирования Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="a08b3-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="a08b3-103">Войдите с помощью **личной учетной записи** (т.е. учетной записи Microsoft) или **рабочей (учебной) учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="a08b3-104">Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="a08b3-105">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="a08b3-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="a08b3-106">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-106">Select **New registration**.</span></span> <span data-ttu-id="a08b3-107">На странице **Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="a08b3-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="a08b3-108">Введите **имя** `ASP.NET Core Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a08b3-108">Set **Name** to `ASP.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="a08b3-109">Введите **поддерживаемые типы учетных записей** для **учетных записей в любом каталоге организаций и личных учетных записей Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="a08b3-110">В разделе **URI адрес перенаправления** введите значение в первом раскрывающемся списке `Web` и задайте значение `https://localhost:5001/`.</span><span class="sxs-lookup"><span data-stu-id="a08b3-110">Under **Redirect URI** , set the first drop-down to `Web` and set the value to `https://localhost:5001/`.</span></span>

    ![Снимок страницы "регистрация приложения"](./images/aad-register-an-app.png)

1. <span data-ttu-id="a08b3-112">Нажмите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-112">Select **Register**.</span></span> <span data-ttu-id="a08b3-113">На странице **Tutorial основного графа ASP.NET** СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="a08b3-113">On the **ASP.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](./images/aad-application-id.png)

1. <span data-ttu-id="a08b3-115">Выберите **Проверка подлинности** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-115">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="a08b3-116">В разделе **URI перенаправления** Добавьте URI со значением `https://localhost:5001/signin-oidc` .</span><span class="sxs-lookup"><span data-stu-id="a08b3-116">Under **Redirect URIs** add a URI with the value `https://localhost:5001/signin-oidc`.</span></span>

1. <span data-ttu-id="a08b3-117">Задайте для параметра **URL-адрес выхода** значение `https://localhost:5001/signout-oidc` .</span><span class="sxs-lookup"><span data-stu-id="a08b3-117">Set the **Logout URL** to `https://localhost:5001/signout-oidc`.</span></span>

1. <span data-ttu-id="a08b3-118">Найдите раздел **Неявное представление** и включите **Маркеры идентификации**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-118">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="a08b3-119">Выберите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-119">Select **Save**.</span></span>

    ![Снимок экрана параметров веб-платформы на портале Azure](./images/aad-web-platform.png)

1. <span data-ttu-id="a08b3-121">Выберите **Сертификаты и секреты** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-121">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="a08b3-122">Нажмите кнопку **Новый секрет клиента**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-122">Select the **New client secret** button.</span></span> <span data-ttu-id="a08b3-123">Введите значение в поле **Описание** и выберите один из вариантов **истечения срока действия** , а затем нажмите кнопку **добавить**.</span><span class="sxs-lookup"><span data-stu-id="a08b3-123">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Снимок экрана: диалоговое окно добавления секрета клиента](./images/aad-new-client-secret.png)

1. <span data-ttu-id="a08b3-125">Скопируйте значение секрета клиента, а затем покиньте эту страницу.</span><span class="sxs-lookup"><span data-stu-id="a08b3-125">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="a08b3-126">Оно вам понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="a08b3-126">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="a08b3-127">Это секрет клиента, он никогда не отображается еще раз, поэтому убедитесь, что вы скопировали его.</span><span class="sxs-lookup"><span data-stu-id="a08b3-127">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Снимок экрана с недавно добавленным секретом клиента](./images/aad-copy-client-secret.png)
