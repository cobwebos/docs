---
title: 选择页面协定-Azure Active Directory B2C |Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中选择页面协定。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4cd29df19179f07fd9b61a2f484b1d49cc05c4cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570575"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>使用自定义策略在 Azure Active Directory B2C 中选择页面协定

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

无论你使用的是用户流还是自定义策略，都可以在你的 Azure Active Directory (Azure AD) B2C 策略中启用 JavaScript 客户端代码。 若要为应用程序中启用 JavaScript，您必须将元素添加到您[自定义策略](active-directory-b2c-overview-custom.md)，选择一个页协定，并使用[b2clogin.com](b2clogin.md)在请求中。 页面协定是 Azure AD B2C 提供的元素与你提供的内容之间的关联。 本文介绍如何通过自定义策略中配置 Azure AD B2C 中选择页面协定。

> [!NOTE]
> 如果你想要为用户流中启用 JavaScript，请参阅[JavaScript 和页协定版本在 Azure Active Directory B2C](user-flow-javascript-overview.md)。

## <a name="replace-datauri-values"></a>替换 DataUri 值

在自定义策略中，可以包含 [ContentDefinitions](contentdefinitions.md)，它定义用户旅程中使用的 HTML 模板。 **ContentDefinition** 包含一个 **DataUri**，它引用 Azure AD B2C 提供的页面元素。 **LoadUri** 是你提供的 HTML 和 CSS 内容的相对路径。

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

要选择页面协定，请在策略中的 [ContentDefinitions](contentdefinitions.md) 中更改 **DataUri** 值。 通过从旧的 **DataUri** 值切换为新值，选择一个不可变包。 使用此包的好处是你知道它不会更改，不会在页面上导致意外的行为。 

若要设置页面协定，请使用下表查找 **DataUri** 值。 

| 旧 DataUri 值 | 新 DataUri 值 |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>后续步骤

有关如何自定义应用程序用户界面的详细信息，请参阅[在 Azure Active Directory B2C 中使用自定义策略自定义应用程序的用户界面](active-directory-b2c-ui-customization-custom.md)。



