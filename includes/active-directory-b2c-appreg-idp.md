---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 560e29be308277aedf8ac4e438f73d4ac5bc417f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475344"
---
若要在 Azure AD B2C 租户中注册应用程序，你可以使用当前**应用程序**体验或我们的新的统一**应用注册（预览版）** 体验。 [了解有关预览版体验的详细信息](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择“应用程序”，然后选择“添加”。
1. 输入应用程序的名称。 例如，*testapp1*。
1. 对于**Web 应用/WEB API**，请选择 **"是"** 。
1. 对于“回复 URL”，请输入`https://jwt.ms`
1. 选择“创建”。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览）](#tab/app-reg-preview/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择 "**应用注册（预览版）** "，然后选择 "**新注册**"。
1. 输入应用程序的**名称**。 例如，*testapp1*。
1. 选择**任何组织目录或任何标识提供者中的帐户**。
1. 在 "**重定向 URI**" 下，选择 " **Web**"，然后在 "URL" 文本框中输入 `https://jwt.ms`。
1. 在 "**权限**" 下，选中 "向*管理员同意 openid 和 offline_access 权限*" 复选框。
1. 选择“注册”。

完成应用程序注册后，启用隐式授权流：

1. 在 "**管理**" 下选择 "**身份验证**"。
1. 选择 **"试用新体验**（如果已显示）"。
1. 在 "**隐式授予**" 下，同时选择 "**访问令牌**" 和 " **ID 令牌**" 复选框。
1. 选择“保存”。