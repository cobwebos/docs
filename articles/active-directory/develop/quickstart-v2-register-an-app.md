---
title: 使用门户向 Azure AD v2.0 终结点注册应用程序 | Microsoft Docs
description: 如何使用 v2.0 终结点向 Microsoft 注册应用以启用登录和访问 Microsoft 服务
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507608"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>如何使用 v2.0 终结点注册应用
要生成同时接受个人 Microsoft 帐户 (MSA) 与工作或学校帐户 (Azure AD) 登录的应用，必须先向 Microsoft 注册应用。 目前，无法使用任何现有的应用搭配 Azure AD 或 MSA - 需要创建一个全新的应用。

> [!NOTE]
> v2.0 终结点并不支持所有 Azure Active Directory 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。


## <a name="visit-the-microsoft-app-registration-portal"></a>访问 Microsoft 应用注册门户
首先，导航到 Microsoft 应用注册门户 ([https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList))。 

使用 Microsoft 个人或工作或学校帐户进行登录。 如果没有任何帐户，请注册新的个人帐户。

完成了吗？ 现在应该看一下 Microsoft 应用列表，该列表有可能一片空白。 让我们改变这点。

单击“添加应用”，并为它命名。 门户将向应用分配全局唯一的应用程序 ID，以便稍后在代码中使用。 如果应用包含的服务器端组件需要用来调用 API（例如：Office、Azure 或自己的 Web API）的访问令牌，则你还需要在此处创建**应用程序密码**。

接下来，添加应用将使用的平台。

* 对于基于 Web 的应用，提供可发送登录消息的**重定向 URI**。
* 对于移动应用，复制系统自动创建的默认重定向 URI。
* 对于 Web API，为自动为你创建一个默认作用域来访问 Web API。 可以选择使用“添加作用域”按钮添加其他作用域。 你还可以使用“预授权应用程序”窗体添加任何已预授权其使用 Web API 的应用程序。 

可以选择在“配置文件”部分中自定义登录页面的外观。 在继续操作之前，请务必单击“保存”。

> [!NOTE]
> 使用 [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 创建应用程序时，系统会在用来登录门户的帐户的主租户中注册该应用程序。 这表示不能使用 Microsoft 个人帐户在 Azure AD 租户中注册应用程序。 如果明确地想要在特定的租户中注册应用程序，请使用最初在该租户中创建的帐户登录。


## <a name="build-a-quickstart-app"></a>构建快速入门应用
现在你已有一个 Microsoft 应用，可以完成 v2.0 快速入门教程之一了。 以下是一些建议：

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

