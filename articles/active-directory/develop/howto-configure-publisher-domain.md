---
title: 配置应用程序的发布服务器域
titleSuffix: Microsoft identity platform
description: 了解如何配置应用程序的发布服务器域，以使用户知道其信息的发送位置。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26ef28be328e01f8edcf898f123db55f262f286c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803342"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>如何：配置应用程序的发布服务器域（预览）

应用程序的发布者域会向用户显示[应用程序的同意提示](application-consent-experience.md)，以让用户知道其信息的发送位置。 在 2019 5 月21日之后注册的多租户应用程序，不具有发布服务器域的服务器显示为未**验证**。 多租户应用程序是支持单个组织目录以外的帐户的应用程序;例如，支持所有 Azure AD 帐户，或支持所有 Azure AD 帐户和个人 Microsoft 帐户。

## <a name="new-applications"></a>新应用程序

注册新应用程序时，应用程序的发布服务器域可能设置为默认值。 此值取决于注册应用的位置，尤其是应用是否已在租户中注册，以及租户是否具有租户验证域。

如果有租户验证的域，应用的发布服务器域将默认为租户的主验证域。 如果没有租户验证的域（在租户中未注册应用程序时），则应用的发布服务器域将设置为 null。

下表汇总了发布服务器域值的默认行为。  

| 租户验证的域 | 发布服务器域的默认值 |
|-------------------------|----------------------------|
| Null | Null |
| *. onmicrosoft.com | *. onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com （主） | domain2.com |

如果未设置多租户应用程序的发布者域，或者将其设置为以. onmicrosoft.com 结尾的域，则应用的许可提示将显示未**验证**的发布者域。

## <a name="grandfathered-applications"></a>Grandfathered 应用程序

如果你的应用在2019到5月21日之前注册，则如果你未设置发布者域，则应用程序的同意提示将不会显示 "未**验证**"。 建议设置发布者域值，以便用户可以在应用的同意提示中看到此信息。

## <a name="configure-publisher-domain-using-the-azure-portal"></a>使用 Azure 门户配置发布服务器域

若要设置应用的发布服务器域，请执行以下步骤。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 如果你的帐户存在于多个 Azure AD 租户中：
   1. 从页面右上角的菜单中选择您的配置文件，然后**切换目录**。
   1. 将会话更改为要在其中创建应用程序的 Azure AD 租户。

1. 导航到[Azure Active Directory > 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)，查找并选择要配置的应用。

   选择应用后，会看到应用的 "**概述**" 页。

1. 在应用的 "**概述**" 页上，选择 "**品牌**" 部分。

1. 查找 "**发布服务器域**" 字段并选择以下选项之一：

   - 如果尚未配置域，请选择 "**配置域**"。
   - 如果已配置域，请选择 "**更新域**"。

如果你的应用已在租户中注册，你将看到两个选项卡可供选择：**选择一个已验证的域**，然后**验证一个新域**。

如果你的应用未在租户中注册，你将只能看到用于验证应用程序的新域的选项。

### <a name="to-verify-a-new-domain-for-your-app"></a>验证应用的新域

1. 创建一个名为 `microsoft-identity-association.json` 的文件并粘贴以下 JSON 代码片段。

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. 用对应于应用的应用程序（客户端） ID 替换占位符 *{应用 id-此处}* 。

1. 将该文件托管在： `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`。 将占位符 *{你的域-此处}* 替换为与验证过的域匹配。

1. 单击 "**验证并保存域**" 按钮。

### <a name="to-select-a-verified-domain"></a>选择已验证的域

- 如果租户已验证域，请从 "**选择已验证的域**" 下拉列表中选择一个域。

>[!Note]
> 应 `application/json`应返回的 "Content-type" 标头。 如果使用其他任何内容（如），则可能会收到如下所述的错误 `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>对应用许可提示的影响

配置发布服务器域会影响用户在应用许可提示中看到的内容。 若要完全了解许可提示的组件，请参阅[了解应用程序许可体验](application-consent-experience.md)。

下表描述了在 2019 5 月21日之前创建的应用程序的行为。

![在 2019 5 月21日之前创建的应用的许可提示](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019在5月21日之后创建的新应用程序的行为将取决于发布服务器域和应用程序的类型。 下表描述了你应在不同的配置组合中看到的更改。

![2019月21日之后创建的应用的许可提示](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>重定向 Uri 的含义

在指定重定向 Uri 时，使用任何工作或学校帐户或个人 Microsoft 帐户（[多租户](single-and-multi-tenant-apps.md)）登录用户的应用程序将受到少数限制。

### <a name="single-root-domain-restriction"></a>单个根域限制

将多租户应用的发布服务器域值设置为 null 时，应用被限制为共享重定向 Uri 的单个根域。 例如，不允许使用以下值组合，因为根域 contoso.com 与 fabrikam.com 不匹配。

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>子域限制

允许使用子域，但必须显式注册根域。 例如，虽然下面的 Uri 共享单个根域，但不允许组合。

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

但是，如果开发人员显式添加根域，则允许组合。

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>例外

以下情况不受单一根域限制的约束：

- 单个租户应用或以单个目录中的帐户为目标的应用
- 使用 localhost 作为重定向 Uri
- 使用自定义方案（非 HTTP 或 HTTPS）重定向 Uri

## <a name="configure-publisher-domain-programmatically"></a>以编程方式配置发布服务器域

目前，不存在以编程方式配置发布服务器域的 REST API 或 PowerShell 支持。
