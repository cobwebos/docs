---
title: 技能集中的自定义 AML 技能
titleSuffix: Azure Cognitive Search
description: Azure 机器学习模型扩展 Azure 认知搜索技能集的功能。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6a3916a41635a1c76bddbb092294f6d362fc6050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924705"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure 认知搜索扩充管道中的 AML 技能

> [!IMPORTANT] 
> 此技能以公共预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持 .NET SDK。

**Aml**技能使你可以使用自定义[Azure 机器学习](../machine-learning/overview-what-is-azure-ml.md) (AML) 模型来扩展 AI 扩充。 一旦 [定型并部署](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)了 aml 模型， **aml** 技能就会将其集成到 AI 扩充中。

与内置技能一样， **AML** 技能具有输入和输出。 输入将以 JSON 对象的形式发送到已部署的 AML 服务，该对象将 JSON 负载作为响应以及成功状态代码输出。 响应应包含 **AML** 技能指定的输出。 其他任何响应都被视为错误，并且不会执行任何扩充。

> [!NOTE]
> 对于从 AML 服务返回的某些标准 HTTP 状态代码，索引器将重试两次。 这些 HTTP 状态代码为：
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>必备条件

* [AML 工作区](../machine-learning/concept-workspace.md)
* 此工作区中具有已[部署模型](../machine-learning/how-to-deploy-azure-kubernetes-service.md)的[AZURE Kubernetes Service AML 计算目标](../machine-learning/concept-compute-target.md)
  * [计算目标应启用 SSL](../machine-learning/how-to-secure-web-service.md#deploy-on-aks-and-field-programmable-gate-array-fpga)。 Azure 认知搜索仅允许访问 **https** 终结点
  * 不能使用自签名证书。

## <a name="odatatype"></a>@odata.type  
Microsoft AmlSkill

## <a name="skill-parameters"></a>技能参数

参数区分大小写。 选择使用哪些参数取决于 [AML 服务所需的身份验证（如果有）](#WhatSkillParametersToUse)

| 参数名称 | 说明 |
|--------------------|-------------|
| `uri` |  (无需[身份验证或密钥身份验证](#WhatSkillParametersToUse)，) _JSON_有效负载将发送到的[AML 服务的评分 URI](../machine-learning/how-to-consume-web-service.md) 。 只允许使用 **https** URI 方案。 |
| `key` | [密钥身份验证](#WhatSkillParametersToUse)所需的 () [AML 服务的密钥](../machine-learning/how-to-consume-web-service.md#authentication-with-keys)。 |
| `resourceId` | [令牌身份验证](#WhatSkillParametersToUse)) 需要 (。 AML 服务的 Azure 资源管理器资源 ID。 它应采用 "订阅/{guid}/resourceGroups/{MachineLearningServices/工作区/{工作区名称}/services/{service_name}" 格式。 |
| `region` | [标记身份验证](#WhatSkillParametersToUse))  (可选。 AML 服务所部署到的 [区域](https://azure.microsoft.com/global-infrastructure/regions/) 。 |
| `timeout` | （可选）如果指定，表明执行 API 调用的 http 客户端的超时值。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 例如，`PT60S` 表示 60 秒。 如果未设置，选择的是默认值 30 秒。 超时可以设置为最大 230 秒和最小 1 秒。 |
| `degreeOfParallelism` | （可选）如果指定了此值，则指示索引器将对你提供的终结点进行的并行调用数。 如果终结点在请求负载过高的情况下失败，则可以减小此值；如果终结点能够接受更多请求并且希望提高索引器的性能，则可以增大此值。  如果未设置，则将使用默认值 5。 DegreeOfParallelism 最大可以设置为10，最小值为1。

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>要使用哪些技能参数

需要哪些 AML 技能参数取决于 AML 服务使用的身份验证（如果有）。 AML 服务提供三个身份验证选项：

* [基于密钥的身份验证](../machine-learning/concept-enterprise-security.md#authentication-for-web-service-deployment)。 提供了一个静态密钥，用于对来自 AML 技能的评分请求进行身份验证
  * 使用 _uri_ 和 _密钥_ 参数
* [基于令牌的身份验证](../machine-learning/concept-enterprise-security.md#authentication)。 [使用基于令牌的身份验证部署](../machine-learning/how-to-deploy-azure-kubernetes-service.md#authentication-with-tokens)AML 服务。 Azure 认知搜索服务的 [托管标识](../active-directory/managed-identities-azure-resources/overview.md) 被授予 AML 服务的工作区中的 "读取者" [角色](../machine-learning/how-to-assign-roles.md) 。 然后，AML 技能使用 Azure 认知搜索服务的托管标识针对 AML 服务进行身份验证，无需任何静态密钥。
  * 使用 _resourceId_ 参数。
  * 如果 Azure 认知搜索服务与 AML 工作区位于不同的区域，请使用 _region_ 参数设置 aml 服务所部署到的区域
* 不进行身份验证。 使用 AML 服务不需要任何身份验证
  * 使用 _uri_ 参数

## <a name="skill-inputs"></a>技能输入

此技能没有“预定义”输入。 你可以选择一个或多个字段，这些字段在此技能的执行时间为输入时已可用，并且发送到 AML 服务的 _JSON_ 有效负载将具有不同的字段。

## <a name="skill-outputs"></a>技能输出

此技能没有“预定义”输出。 根据 AML 服务将返回的响应，添加输出字段，以便可以从 _JSON_ 响应中选取它们。

## <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>示例输入 JSON 结构

此 _JSON_ 结构表示将发送到 AML 服务的有效负载。 结构的顶级字段将对应于在技能定义的部分中指定的 "名称" `inputs` 。 这些字段的值将来自这些字段的 `source`（可能来自文档中的字段，也可能来自另一个技能）

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>示例输出 JSON 结构

输出对应于从 AML 服务返回的响应。 AML 服务只应通过查看响应标头来返回 (验证的 _JSON_ 有效负载 `Content-Type`) 并且应为根据与中的 "名称" 匹配 `output` 且值被视为扩充的对象。

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>内联成形示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>内联成形输入 JSON 结构

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>内联造型示例输出 JSON 结构

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>错误案例
除了 AML 不可用或未成功发送的状态代码，以下内容被视为错误情况：

* 如果 AML 服务返回成功状态代码，但响应指示它不是 `application/json` ，则响应将被视为无效，不会执行任何根据。
* 如果 AML 服务返回无效的 json

如果 AML 服务不可用或返回 HTTP 错误，将在索引器执行历史记录中添加一个友好错误，其中包含有关 HTTP 错误的任何可用详细信息。

## <a name="see-also"></a>另请参阅

+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [AML 服务故障排除](../machine-learning/how-to-troubleshoot-deployment.md)