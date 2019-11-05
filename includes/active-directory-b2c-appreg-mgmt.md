---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: baf39c1fe72703d8ebc18b03bae1c963536d7ced
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475194"
---
若要在 Azure AD B2C 租户中注册应用程序，你可以使用当前**应用程序**体验或我们的新的统一**应用注册（预览版）** 体验。 [了解有关预览版体验的详细信息](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure Active Directory”（而不是“Azure AD B2C”）。 或者选择“所有服务”，然后搜索并选择“Azure Active Directory”。
1. 在“管理”下，选择“应用注册(旧版)”
1. 选择“新建应用程序注册”。
1. 输入应用程序的名称。 例如，*managementapp1*。
1. 对于“应用程序类型”，请选择“Web 应用/API”。
1. 在“登录 URL”中输入任何有效的 URL。 例如，`https://localhost`。 此终结点不需要可访问，但必须是有效的 URL。
1. 选择“创建”。
1. 记下**已注册的应用**“概述”页上显示的**应用程序 ID**。 在稍后的步骤中会使用此值。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览）](#tab/app-reg-preview/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择 "**应用注册（预览版）** "，然后选择 "**新注册**"。
1. 输入应用程序的**名称**。 例如，*managementapp1*。
1. **仅选择此组织目录中的帐户**。
1. 在 "**权限**" 下，清除 "*授予对 openid 和 offline_access 权限的管理员同意*" 复选框。
1. 选择“注册”。
1. 记录应用程序概述页上显示的**应用程序（客户端） ID** 。 在稍后的步骤中会使用此值。