---
title: "密码复杂性 - Azure AD B2C | Microsoft 文档"
description: "如何配置由 Azure Active Directory B2C 中的使用者提供的密码复杂性要求"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: ab5547ef76121aa395168844bd69759613ffc045
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---

# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C：配置密码复杂性要求

Azure Active Directory B2C (Azure AD B2C) 支持更改由最终用户在创建帐户时提供的密码复杂性要求。  默认情况下，Azure AD B2C 使用 `Strong` 密码。  此外，Azure AD B2C 还支持用于控制客户可以使用的密码复杂性的配置选项。

## <a name="when-password-rules-are-enforced"></a>何时强制实施密码规则

在注册或密码重置期间，最终用户必须提供符合复杂性规则的密码。  根据策略要求，需强制实施密码复杂性规则。  在注册时，一个策略可能需要一个 4 位数的 pin，而另一个策略在注册时需要一个 8 字符的字符串。  例如，你可以使用针对成人（而非儿童）的不同密码复杂性的策略。

在登录期间绝不会强制实施密码复杂性。  登录时不会提示用户更改密码，因为它不符合当前的复杂性要求。

以下是可在其中配置密码复杂性的策略的类型：

* 注册或登录策略
* 密码重置策略
* 自定义策略（[在自定义策略中配置密码复杂性](active-directory-b2c-reference-password-complexity-custom.md)）

## <a name="how-to-configure-password-complexity"></a>如何配置密码复杂性

1. 请按照以下步骤[导航到 Azure AD B2C 设置](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
1. 打开“注册或登录策略”。
1. 选择一个策略，然后单击“编辑”。
1. 打开“密码复杂性”。
1. 将此策略的密码复杂性更改为“简单”、“强”或“自定义”。

### <a name="comparison-chart"></a>比较图表

| 复杂性 | 说明 |
| --- | --- |
| 简单 | 为至少 8 到 64 个字符的密码。 |
| 强 | 为至少 8 到 64 个字符的密码。 它需要 4 个小写字母、大写字母、数字或符号中的 3 个。 |
| “自定义” | 此选项提供了对密码复杂性规则的最大控制。  可以配置自定义长度。  还可以接受仅为数字的密码 (pin)。 |

## <a name="options-available-under-custom"></a>自定义下的可用选项

### <a name="character-set"></a>字符集

允许你接受仅为数字 (pin) 或完整的字符集。

* “仅数字”仅限输入数字 (0-9) 密码。
* “所有”则允许任何字母、数字或符号。

### <a name="length"></a>Length

允许你控制密码的长度要求。

* 最小长度必须至少为 4。
* 最大长度必须大于或等于最小长度，最多可包含 64 个字符。

### <a name="character-classes"></a>字符类

允许你控制密码中使用的不同字符类型。

* 4 选 2：小写字符、大写字符、数字 (0-9)、符号确保密码包含至少两种字符类型。 例如，数字和小写字符。
* 4 选 3：小写字符、大写字符、数字 (0-9)、符号确保密码包含至少三种字符类型。 例如，数字、小写字符和大写字符。
* 全部 4 个：小写字符、大写字符、数字 (0-9)、符号确保密码包含所有字符类型。

    > [!NOTE]
    > 要求“全部 4 个”可能会对最终用户造成困扰。 某些研究表明此要求不会改善密码熵。 请参阅 [NIST 密码指南](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

