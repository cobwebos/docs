---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: dec912ef6479e3668f22227c5f05de5ae8b12fcc
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799641"
---
若要在 Azure AD B2C 租户中注册应用程序，你可以使用当前**应用程序**体验或我们的新的统一**应用注册（预览版）** 体验。 [详细了解此新体验](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择“应用程序”，然后选择“添加”。
1. 输入应用程序的名称。 例如，*ROPC_Auth_app*。
1. 对于**本机客户端**，选择“是”。
1. 保留其他值不变，然后选择“创建”。
1. 记录**应用程序 ID** ，以便在后面的步骤中使用。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览）](#tab/app-reg-preview/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择 "**应用注册（预览版）** "，然后选择 "**新注册**"。
1. 输入应用程序的**名称**。 例如，*ROPC_Auth_app*。
1. 保留其他值，然后选择 "**注册**"。
1. 记录**应用程序（客户端） ID** ，以便在后面的步骤中使用。
1. 在 "**管理**" 下选择 "**身份验证**"。
1. 选择 **"试用新体验**（如果已显示）"。
1. 在 "**默认客户端类型**" 下，选择 **"是"** 以将应用程序视为公共客户端。 此设置是 ROPC 流所必需的。
1. 选择“保存”。