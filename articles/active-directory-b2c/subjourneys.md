---
title: Azure Active Directory B2C 中的 SubJourneys |Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 SubJourneys 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6609dabe9bd507751bd131a4effe24295e2aac04
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952434"
---
# <a name="subjourneys"></a>SubJourneys

Subjourneys 可用于组织和简化用户旅程中的业务流程步骤流。 [用户旅程](userjourneys.md)指定策略允许信赖方应用程序为用户获取所需声明的显式路径。 用户通过这些路径检索要提供给信赖方的声明。 换言之，用户旅程定义最终用户在 Azure AD B2C 标识体验框架处理请求时所经历的业务逻辑。 用户旅程表示为成功事务必须遵循的业务流程序列。 业务流程步骤的 [ClaimsExchange](userjourneys.md#claimsexchanges) 元素绑定到执行的单个 [技术配置文件](technical-profiles-overview.md) 。

Subjourney 是可在用户旅程中的任何时刻调用的业务流程步骤的分组。 您可以使用 subjourneys 创建可重用的步骤序列或实现分支以更好地表示业务逻辑。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>用户旅程分支

Subjourneys 的行为类似于 [用户旅程](userjourneys.md)，因为这两者都表示为成功完成事务所必须遵循的业务流程序列。 用户旅程可以自行调用，需要执行 SendClaims 步骤。 Subjourneys 是用户旅程的组件，不能独立调用，始终从用户旅程中调用。

分支的关键组件是允许在用户旅程中进行更好的业务逻辑处理。 常见的业务流程步骤分组为单独调用的各个部分。 Subjourney 可以简化将多个业务流程步骤结合在一起 (具有相同前提条件) 的旅程。 Subjourney 仅在用户旅程中调用，它不应调用另一个 subjourney。

有两种类型的 subjourneys：

- **调用** -将控制权返回给调用方。 执行 subJourney，然后将控制权返回给用户旅程中当前正在执行的业务流程步骤。
- **传输** -将控制转移到 subjourney (不可逆的分支) 。 Subjourney 必须具有 SendClaims 步骤，才能将声明返回给信赖方应用程序。

## <a name="example-scenarios"></a>方案示例

### <a name="call-subjourney"></a>调用 SubJourney

调用 SubJourney 在以下方案中很有用：

- 年龄门：对于年龄门，用户旅程有很多共享组件。 分支允许将公共元素编译为可共享的组件。  
- 家长同意：在家长同意设计中，通过允许我们从用户旅程的 "已执行" 的声明中访问声明，使其能够在查找用户需要同意后进入家长许可用户旅程，从而为您提供便利。 
- 注册登录：考虑这样一种情况：用户已在目录中存在，但可能忘记了用户已创建帐户。 在这种情况下，可能需要这样做，而不是告诉用户他们所输入的凭据已经存在，并强制用户重新启动旅程，以便策略能够从注册流到该用户的登录流执行切换。  

### <a name="transfer-subjourney"></a>传输 SubJourney

传输 SubJourney 在以下方案中很有用：

- 显示块页。
- A/B 测试，通过将请求路由到 SubJourney 来执行和颁发令牌。

## <a name="adding-a-subjourney-element"></a>添加 SubJourney 元素

下面是类型为的元素的一个示例 `SubJourney` `Call` ，它将控制权返回给用户旅程。

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

下面是类型为的元素的一个示例 `SubJourney` `Transfer` ，它将一个令牌返回给信赖方应用程序。

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-subjourney-step"></a>调用 subjourney 步骤

类型为的新业务流程步骤 `InvokeSubJourney` 用于执行 subjourney。 下面是一个示例，演示了此业务流程步骤的所有执行元素。

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Subjourney 不应调用另一个 subjourney。

## <a name="components"></a>组件

若要定义策略支持的 subjourneys，请在策略文件的顶级元素下添加一个 **subjourneys** 元素。

**SubJourneys**元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | 定义完整用户流所需的所有构造的 subjourney。 |

**SubJourneys**元素包含以下属性：

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用户旅程在策略中引用 SubJourney 时可以使用的 SubJourney 标识符。 [候选](userjourneys.md#journeylist)元素的**SubJourneyReferenceId**元素指向此属性。 |
| 类型 | 是 | 可能的值：`Call` 或 `Transfer`。 有关详细信息，请参阅 [用户旅程分支](#user-journey-branching)|

**SubJourney**元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 成功事务必须遵循的业务流程序列。 每个用户旅程都包含按顺序执行的业务流程步骤的有序列表。 如果任何步骤失败，则事务将失败。 |

## <a name="orchestrationsteps"></a>OrchestrationSteps

有关业务流程步骤元素的完整列表，请参阅 [UserJourneys](userjourneys.md)。

## <a name="next-steps"></a>后续步骤

[UserJourneys](userjourneys.md)