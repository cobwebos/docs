---
title: 配置应用程序的发行者域 |Azure
description: 了解如何配置应用程序的发行者域，让用户了解其信息发送位置。
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502273"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>如何：配置应用程序的发行者域 （预览版）

应用程序的发行者域向用户显示上[应用程序的许可提示](application-consent-experience.md)，让用户了解其信息发送位置。 多租户应用程序后，2019 年 5 月 21 日注册没有发布服务器域名显示为**未经验证**。 多租户应用程序都支持单个组织目录中; 外部帐户的应用程序例如，支持所有 Azure AD 帐户，或支持所有 Azure AD 帐户和个人 Microsoft 帐户。

## <a name="new-applications"></a>新的应用程序

注册新的应用程序时，您的应用程序发行者域可能设置为默认值。 值取决于注册应用程序所在，特别是是否在租户中注册应用和租户具有租户是否已验证的域。

如果租户已验证域，应用程序的发行者域将默认为租户的主域已验证。 如果有没有租户已验证域 （这是当未在租户中注册应用程序时），应用程序的发行者域将被设置为 null。

下表总结了发布服务器的域值的默认行为。  

| 租户已验证域 | 默认值为发行者域 |
|-------------------------|----------------------------|
| Null | Null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-domain2.com （主） | domain2.com |

如果未设置多租户应用程序的发布服务器域，或如果设置为域中结束。 onmicrosoft.com，应用程序的许可提示将显示**未经验证**代替发行者域。

## <a name="grandfathered-applications"></a>Grandfathered 应用程序

如果您的应用程序已注册，2019 年 5 月 21 日之前将不会显示应用程序的许可提示**未经验证**如果你尚未设置发行者域。 我们建议设置发布服务器的域值，以便用户可以在应用程序的许可提示上看到此信息。

## <a name="configure-publisher-domain-using-the-azure-portal"></a>使用 Azure 门户的发布服务器域配置

若要设置应用的发行者域，请执行以下步骤。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 如果你的帐户存在多个 Azure AD 租户中：
   1. 从在页上，在右上角菜单中选择你的配置文件，然后**切换目录**。
   1. 将你的会话更改为你想要创建你的应用程序的 Azure AD 租户。

1. 导航到[Azure Active Directory > 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)查找并选择你想要配置的应用。

   选择应用后，会看到应用的**概述**页。

1. 从应用程序的**概述**页上，选择**品牌**部分。

1. 查找**发行者域**字段中，选择下列选项之一：

   - 选择**配置域**如果还没有已配置一个域。
   - 选择**更新域**如果域已配置。

如果在租户中注册您的应用程序，则可以看到两个选项卡从中进行选择：**选择已验证的域**并**验证新域**。

如果您的应用程序未在租户中注册，则只能看到验证你的应用程序的新域的选项。

### <a name="to-verify-a-new-domain-for-your-app"></a>若要验证您的应用程序的新域

1. 创建名为`microsoft-identity-association.json`并粘贴以下 JSON 代码片段。

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. 替换占位符 *{YOUR-应用-ID-此处}* 对应于您的应用程序的应用程序 （客户端） id。

1. 在将文件存放： `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`。 替换占位符 *{YOUR 域此处}* 以匹配已验证的域。

1. 单击**验证和保存域**按钮。

### <a name="to-select-a-verified-domain"></a>若要选择已验证的域

- 如果你的租户具有已验证的域，选择从域之一**选择一个已验证的域**下拉列表。

## <a name="implications-on-the-app-consent-prompt"></a>对应用程序的影响许可提示

配置发布服务器域会影响应用程序许可提示上显示的内容。 若要完全了解许可提示的组件，请参阅[了解应用程序许可体验](application-consent-experience.md)。

下表介绍在 2019 年 5 月 21 日之前, 创建的应用程序的行为。

![2019 5 月 21 日之前创建的应用的许可提示](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019 5 月 21 日之后创建的新应用程序的行为取决于发布服务器域和应用程序的类型。 下表描述了你应该会看到与配置的不同组合的更改。

![许可提示，2019 年 5 月 21 日之后创建的应用](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>对重定向 Uri 的影响

在具有任何工作或学校帐户或个人 Microsoft 帐户的用户登录的应用程序 ([多租户](single-and-multi-tenant-apps.md)) 是受几个限制指定时重定向 Uri。

### <a name="single-root-domain-restriction"></a>单个根域限制

如果多租户应用的发布服务器域值设置为 null，应用只限于共享单个根域重定向 Uri。 例如，不允许以下值的组合，因为根域、 contoso.com、 fabrikam.com 与不匹配。

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>子域限制

允许子域，但必须显式注册的根域。 例如，以下 Uri 共享单个根域，而不允许组合。

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

但是，如果开发人员显式添加根级域，则允许组合。

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>例外

在以下情况下不受单个根域限制：

- 单租户应用或目标的单个目录中的帐户的应用
- 使用的本地主机作为重定向 Uri
- 使用自定义方案 （非 HTTP 或 HTTPS） 的重定向 Uri

## <a name="configure-publisher-domain-programmatically"></a>以编程方式配置发布服务器域

目前，没有 REST API 或 PowerShell 支持以编程方式配置发布服务器的域。
