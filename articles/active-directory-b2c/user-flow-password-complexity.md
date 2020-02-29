---
title: 配置密码复杂性要求
titleSuffix: Azure AD B2C
description: 如何配置由 Azure Active Directory B2C 中的使用者提供的密码复杂性要求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185635"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>配置 Azure Active Directory B2C 中的密码复杂性要求

Azure Active Directory B2C (Azure AD B2C) 支持更改由最终用户在创建帐户时提供的密码复杂性要求。 默认情况下，Azure AD B2C 使用 `Strong` 密码。 此外，Azure AD B2C 还支持用于控制客户可以使用的密码复杂性的配置选项。

## <a name="password-rule-enforcement"></a>密码规则强制实施

在注册或密码重置期间，最终用户必须提供符合复杂性规则的密码。 根据用户流要求，需强制实施密码复杂性规则。 在注册期间，有一个用户流可能需要四位数的 pin，而另一个用户流在注册过程中需要8个字符串。 例如，可以使用针对成人（而非儿童）的不同密码复杂性的用户流。

在登录期间绝不会强制实施密码复杂性。 登录时不会提示用户更改密码，因为它不符合当前的复杂性要求。

可在以下类型的用户流中配置密码复杂性：

- 注册或登录用户流
- 密码重置用户流

如果使用自定义策略，可以（[在自定义策略中配置密码复杂性](custom-policy-password-complexity.md)）。

## <a name="configure-password-complexity"></a>配置密码复杂性

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在门户工具栏中选择 "**目录 + 订阅**" 图标，然后选择包含 Azure AD B2C 租户的目录。
3. 在 Azure 门户中，搜索并选择 " **Azure AD B2C**"。
4. 选择**用户流（策略）** 。
2. 选择一个用户流，然后单击“属性”。
3. 在“密码复杂性”下，将此用户流的密码复杂性更改为“简单”、“强”或“自定义”。

### <a name="comparison-chart"></a>比较图表

| 复杂性 | 说明 |
| --- | --- |
| 简单 | 为至少 8 到 64 个字符的密码。 |
| 非常 | 为至少 8 到 64 个字符的密码。 它需要 4 个小写字母、大写字母、数字或符号中的 3 个。 |
| 自定义 | 此选项提供了对密码复杂性规则的最大控制。  可以配置自定义长度。  还可以接受仅为数字的密码 (pin)。 |

## <a name="custom-options"></a>自定义选项

### <a name="character-set"></a>字符集

允许你接受仅为数字 (pin) 或完整的字符集。

- “仅数字”仅限输入数字 (0-9) 密码。
- “所有”则允许任何字母、数字或符号。

### <a name="length"></a>长度

允许你控制密码的长度要求。

- 最小长度必须至少为 4。
- 最大长度必须大于或等于最小长度，最多可包含 64 个字符。

### <a name="character-classes"></a>字符类

允许你控制密码中使用的不同字符类型。

- 4 选 2：小写字符、大写字符、数字 (0-9)、符号确保密码包含至少两种字符类型。 例如，数字和小写字符。
- **3 （共4个）：小写字符、大写字符、数字（0-9）、符号**确保密码包含至少三个字符类型。 例如，数字、小写字符和大写字符。
- 全部 4 个：小写字符、大写字符、数字 (0-9)、符号确保密码包含所有字符类型。

    > [!NOTE]
    > 要求“全部 4 个”可能会对最终用户造成困扰。 某些研究表明此要求不会改善密码熵。 请参阅 [NIST 密码指南](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
