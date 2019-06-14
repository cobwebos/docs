---
title: BuildingBlocks - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 BuildingBlocks 元素。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 67770661f0660b9a84d16bbbc7d86cfcbca2cfdf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511587"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

BuildingBlocks  元素添加到 [TrustFrameworkPolicy](trustframeworkpolicy.md) 元素中。

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
 </BuildingBlocks>
```

BuildingBlocks  元素包含以下元素，必须按照定义的顺序指定：

- [ClaimsSchema](claimsschema.md) - 定义了可以引用为策略的一部分的声明类型。 声明架构是发出声明类型的位置。 声明类型类似于许多编程语言中的变量。 可以使用声明类型从应用程序的用户那里收集数据、接收来自社交标识提供者的声明、发送和接收来自自定义 REST API 的数据，也可以存储自定义策略使用的任何内部数据。 

- [Predicates 和 PredicateValidationsInput](predicates.md) - 可用于执行验证过程，以确保仅向声明输入格式正确的数据。
 
- [ClaimsTransformations](claimstransformations.md) - 包含可在策略中使用的一系列声明转换。  声明转换可以将一个声明转换为另一个声明。 在声明转换中，可以指定转换方法，如： 
    - 将字符串声明的大小写更改为指定的大小写。 例如，将字符串从小写更改为大写。
    - 比较两个声明并返回带 true 值的声明，指示声明匹配，否则为 false。
    - 基于策略中提供的参数创建字符串声明。
    - 使用随机数生成器创建随机字符串。
    - 根据提供的格式字符串格式化声明。 此转换将使用 C# `String.Format` 方法。

- [ContentDefinitions](contentdefinitions.md) - 包含在用户旅程中使用的 HTML5 模板的 URL。 在自定义策略中，内容定义定义用于用户旅程中指定 UI 步骤的 HTML5 页面 URI。 例如，登录或注册、密码重置或错误页面。 可以通过重写 HTML5 文件的 LoadUri 来修改外观。 也可根据需要创建新的内容定义。 此元素可能包含使用本地化 ID 的本地化资源引用。

- [Localization](localization.md) - 使你能够支持多种语言。 策略中的本地化支持允许你在策略中设置支持语言列表并选择默认语言。 此外支持特定于语言的字符串和集合。


