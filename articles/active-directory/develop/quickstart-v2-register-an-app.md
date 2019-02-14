---
title: 使用 Azure AD v2.0 终结点注册应用 | Microsoft Docs
description: 了解如何使用 Azure AD v2.0 终结点向 Microsoft 注册应用以启用登录和访问 Microsoft 服务。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0e2980b48a5d9b1f2e9d75e59a27bfceb0d1180
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195577"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>快速入门：向 Azure Active Directory v2.0 终结点注册应用

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

要生成同时接受个人 Microsoft 帐户 (MSA) 和工作或学校帐户 (Azure AD) 登录的应用，将需要使用 Azure Active Directory (Azure AD) v2.0 终结点注册应用。 目前，无法使用任何现有的应用搭配 Azure AD 或 MSA - 需要创建一个全新的应用。

请注意，v2.0 终结点并非支持所有 Azure AD 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。

> [!NOTE]
> 注册新应用？ 在 Azure 门户中试用新的**应用注册（预览版）** 体验。 请参阅[注册应用（预览版）](quickstart-register-app.md)以开始使用。

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>步骤 1：登录到 Microsoft 应用程序注册门户

1. 导航到 Microsoft 应用注册门户 [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。
1. 使用 Microsoft 个人或工作或学校帐户进行登录。 如果没有任何帐户，请注册新的个人帐户。
1. 完成了吗？ 现在应该看一下 Microsoft 应用列表，该列表有可能一片空白。 让我们改变这点。

## <a name="step-2-register-an-app"></a>步骤 2：注册应用程序

1. 选择“添加应用”，并为它命名。
    门户将向应用分配全局唯一的应用程序 ID，以便稍后在代码中使用。 如果应用包含的服务器端组件需要用来调用 API（例如：Office、Azure 或自己的 Web API）的访问令牌，则你还需要在此处创建**应用程序密码**。
1. 接下来，添加应用将使用的平台。
    * 对于基于 Web 的应用，提供可发送登录消息的**重定向 URI**。
    * 对于移动应用，复制系统自动创建的默认重定向 URI。
    * 对于 Web API，为自动为你创建一个默认作用域来访问 Web API。
        可以使用“添加作用域”按钮添加其他作用域。 你还可以使用“预授权应用程序”窗体添加任何已预授权其使用 Web API 的应用程序。
1. （可选）自定义“配置文件”部分中登录页面的外观。 
1. “保存”所做的更改，然后继续。

> [!NOTE]
> 使用 [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 注册应用程序时，系统会在用来登录门户的帐户的主租户中注册该应用程序。 这表示不能使用 Microsoft 个人帐户在 Azure AD 租户中注册应用程序。 如果明确地想要在特定的租户中注册应用程序，请使用最初在该租户中创建的帐户登录。

## <a name="next-steps"></a>后续步骤

现在已有 Microsoft 应用，可以完成一个 v2.0 快速入门 2。 以下是一些建议：

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]
