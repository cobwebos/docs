---
title: 教程-注册应用程序-Azure Active Directory B2C
description: 了解如何使用 Azure 门户在 Azure Active Directory B2C 中注册 Web 应用程序。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5c46d3153bdc5768836bce198af115f82e8469f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056283"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中注册应用程序

必须在你管理的租户中注册应用程序，然后这些[应用程序](active-directory-b2c-apps.md)才能与 Azure Active Directory (Azure AD) B2C 交互。 本教程介绍如何使用 Azure 门户注册 Web 应用程序。

在本文中，学习如何：

> [!div class="checklist"]
> * 注册 Web 应用程序
> * 创建客户端机密

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

如果尚未创建自己的 [Azure AD B2C 租户](tutorial-create-tenant.md)，请立即创建一个。 可以使用现有的 Azure AD B2C 租户。

## <a name="register-a-web-application"></a>注册 Web 应用程序

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
3. 选择“应用程序”，然后选择“添加”   。
4. 输入应用程序的名称。 例如，“webapp1”  。
5. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。   
6. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点  。 例如，您可以将其设置为本地侦听`https://localhost:44316`。 如果尚不知道端口号，可以先输入占位符值，以后再进行更改。

    用于测试目的，例如本教程您可以将其设置为`https://jwt.ms`后者将显示有关检查令牌的内容。 对于本教程中，设置**回复 URL**到`https://jwt.ms`。

    回复 URL 必须以方案 `https` 开头，并且所有回复 URL 值必须共享一个 DNS 域。 例如，如果应用程序的回复 URL 为 `https://login.contoso.com`，则可以像此 URL `https://login.contoso.com/new` 一样向其添加。 或者，你可以引用 `login.contoso.com` 的 DNS 子域，例如 `https://new.login.contoso.com`。 若要将包含 `login-east.contoso.com` 和 `login-west.contoso.com` 的应用程序作为回复 URL，则必须按以下顺序添加这些回复 URL：`https://contoso.com`、`https://login-east.contoso.com`、`https://login-west.contoso.com`。 可以添加后两个回复 URL，因为它们是第一个回复 URL (`contoso.com`) 的子域。

7. 单击**创建**。

## <a name="create-a-client-secret"></a>创建客户端机密

如果你的应用程序交换令牌的代码，您需要创建应用程序密码。

1. 在中**Azure AD B2C 的应用程序**页上，选择你创建的应用程序*webapp1*。
2. 选择**密钥**，然后选择**生成密钥**。
3. 选择“保存”以查看密钥。  记下“应用密钥”值  。 使用此值作为应用程序代码中的应用程序机密。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 注册 Web 应用程序
> * 创建客户端机密

接下来，了解如何创建用户流，以便让用户注册、 登录，并管理其配置文件。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中创建用户流 >](tutorial-create-user-flows.md)
