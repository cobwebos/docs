---
title: 添加 Azure AD 库应用程序时的问题 | Microsoft Docs
description: 了解添加 Azure AD 库应用程序时常见的问题，以及可以执行哪些操作来解决这些问题
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 5cb43976c17bfd46d1f16f0bdaff1c164bdc0fb6
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34053753"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>添加 Azure AD 库应用程序时的问题

本文帮助你了解当添加 Azure AD 库应用程序时面临的常见问题以及如何加以解决。

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>单击“添加”按钮后，应用程序要很长时间才显示

某些情况下，将应用程序添加到目录后，可能需要 1-2 分钟（有时更久）才会显示该应用程序。 尽管这不是正常的预期结果，但是通过单击 [Azure 门户](https://portal.azure.com/)右上角的“通知”图标（铃铛），并查找标签为“创建应用程序”的“正在进行中”或“已完成”通知，可以看到应用程序添加正在进行中。

如果应用程序未成功添加，或在单击“添加”按钮时遇到错误，将看到一个处于“错误”状态的**通知**。 如果想要了解有关错误的详细信息或将其与支持工程师共享，请按照[如何查看门户通知的详细信息](#how-to-see-the-details-of-a-portal-notification)部分中的步骤进行操作。

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>单击"添加"按钮后，应用程序未显示

有时，由于暂时性问题、网络问题或 bug，添加应用程序会失败。 如果单击 Azure 门户右上角的“通知”图标（铃铛）后，看到“创建应用程序”通知旁边有一个红色的 (!) 图标，则说明添加失败。 这表明创建应用程序时出现了错误。

如果单击“添加”按钮时遇到错误，将看到一个处于“错误”状态的**通知**。 如果想要了解有关错误的详细信息或将其与支持工程师共享，请按照[如何查看门户通知的详细信息](#how-to-see-the-details-of-a-portal-notification)部分中的步骤进行操作。

 ## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>添加应用程序后不知道如何进行设置

如果需要有关应用程序的帮助，可先行参阅[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)一文。

除此之外，[Azure AD 应用程序文档库](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index)可帮助你深入了解如何使用 Azure AD 进行单一登录及其工作原理。

## <a name="how-to-see-the-details-of-a-portal-notification"></a>如何查看门户通知的详细信息

可以通过遵循以下步骤来查看任何门户通知的详细信息：

1.  单击 Azure 门户右上角的“通知”图标（铃铛）

2.  选择状态为“错误”的任何通知（旁边有红色的 (!)）。

    >[!NOTE]
    >不能单击状态为“成功”或“正在进行中”的通知。
    >
    >

4.  使用“通知详细信息”**下的信息了解有关问题的详细信息。

5.  如果仍需要帮助，还可以与支持工程师或产品组共享此信息以获取有关问题的帮助。

6.  单击“复制错误”文本框右侧的“复制”**图标**，复制所有通知详细信息以与支持工程师或产品组工程师共享

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>如何通过向支持工程师发送通知详细信息来获取帮助

如果需要帮助，与支持工程师共享**下面列出的所有详细信息**非常重要，这有助于他们迅速提供帮助。 通过**捕获屏幕截图**或单击“复制错误”图标（位于“复制错误”文本框右侧），可以轻松执行此操作。

## <a name="notification-details-explained"></a>介绍通知详细信息

有关通知的更多详细信息，请参阅以下说明。

### <a name="essential-notification-items"></a>基本通知项

-   **标题** – 通知的描述性标题

  * 示例 – **应用程序代理设置**

-   **说明** – 由操作导致的所发生情况的说明

    -   示例 – **输入的内部 URL 已被其他应用程序使用**

-   **通知 ID** – 通知的唯一 ID

    -   示例 - **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **客户端请求 ID** – 由浏览器发出的特定请求 ID

    -   示例 – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **时间戳 UTC** – 在通知发生期间的时间戳 (UTC)

    -   示例 – **2017-03-23T19:50:43.7583681Z**

-   **内部事务 ID** – 可用于在我们系统中查找错误的内部 ID

    -   示例 – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – 执行操作的用户

    -   示例 – **tperkins@f128.info**

-   **租户 ID** – 执行操作的用户所属的租户的唯一 ID

    -   示例 – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **用户对象 ID** – 执行操作的用户的唯一 ID

    -   示例 – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>详细通知项

-   **显示名称** – **（可以为空）** 错误的更详细的显示名称

    -   示例 – **应用程序代理设置**

-   **状态** – 通知的特定状态

    -   示例 – **失败**

-   **对象 ID** – **（可能为空）** 对其执行了操作的对象 ID

    -   示例 – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **详细信息** – 由操作导致的所发生情况的详细说明

    -   示例 - **内部 URL“http://bing.com/”无效，因为它已在使用中**

-   **复制错误** – 单击“复制错误”文本框右侧的复制图标，可复制所有通知详细信息以便与支持小组或产品小组共享 
-   工程师

    -   示例 ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](manage-apps/what-is-application-management.md)
