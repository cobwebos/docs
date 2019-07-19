---
title: 选择页面布局-Azure Active Directory B2C
description: 了解如何在 Azure Active Directory B2C 中选择页面布局。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 011fb262ff91c56269c5b7dc9adf4aaeab9acbd5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229045"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>使用自定义策略在 Azure Active Directory B2C 中选择页面布局

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

无论你使用的是用户流还是自定义策略, 你都可以在 Azure Active Directory (Azure AD) B2C 策略中启用 JavaScript 客户端代码。 若要为应用程序启用 JavaScript, 则必须将元素添加到[自定义策略](active-directory-b2c-overview-custom.md), 选择页面布局, 并在请求中使用[b2clogin.com](b2clogin.md) 。

页面布局是 Azure AD B2C 提供的元素与提供的内容之间的关联。

本文介绍如何通过在自定义策略中配置页面布局, 在 Azure AD B2C 中进行选择。

> [!NOTE]
> 如果要为用户流启用 JavaScript, 请参阅[Azure Active Directory B2C 中的 javascript 和页面布局版本](user-flow-javascript-overview.md)。

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

若要选择页面布局, 请在策略中更改[ContentDefinitions](contentdefinitions.md)中的**DataUri**值。 通过从旧的 **DataUri** 值切换为新值，选择一个不可变包。 使用此包的好处是你知道它不会更改，不会在页面上导致意外的行为。

若要设置页面布局, 请使用下表查找**DataUri**值。

| 旧 DataUri 值 | 新 DataUri 值 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>版本更改日志

页面布局包会定期更新, 以包括其页面元素中的修复和改进。 以下更改日志指定了每个版本中引入的更改。

### <a name="110"></a>1.1.0

- 异常页面 (globalexception)
  - 辅助功能修复
  - 当策略中没有联系人时删除默认消息
  - 已删除默认 CSS
- MFA 页面 (多因素)
  - 已删除 "确认代码" 按钮
  - 代码的输入字段现在只能输入最多六个字符
  - 当输入6位代码时, 此页将自动尝试验证输入的代码, 而无需单击任何按钮
  - 如果代码错误, 则会自动清除输入字段
  - 三 (3) 次尝试错误代码后, B2C 会将错误发送回信赖方
  - 辅助功能修补程序
  - 已删除默认 CSS
- 自断言页面 (selfasserted)
  - 删除取消警报
  - Error 元素的 CSS 类
  - 显示/隐藏错误逻辑改进
  - 已删除默认 CSS
- 统一 SSP (unifiedssp)
  - 添加了 "使我保持登录 (KMSI)" 控件

### <a name="100"></a>1.0.0

- 初始版本

## <a name="next-steps"></a>后续步骤

有关如何自定义应用程序用户界面的详细信息，请参阅[在 Azure Active Directory B2C 中使用自定义策略自定义应用程序的用户界面](active-directory-b2c-ui-customization-custom.md)。
