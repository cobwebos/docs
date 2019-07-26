---
title: 概述-自动部署 Azure 逻辑应用
description: 了解 Azure 资源管理器模板以便自动部署 Azure 逻辑应用
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: f2c6676284e8ed58f1626ab824aa7a7c9c456a31
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494458"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>概述：使用 Azure 资源管理器模板自动部署 Azure 逻辑应用

准备好自动创建和部署逻辑应用时, 可以将逻辑应用的基础工作流定义扩展到[Azure 资源管理器模板](../azure-resource-manager/resource-group-overview.md)。 此模板定义了用于预配和部署逻辑应用的基础结构、资源、参数和其他信息。 通过为不同于部署的值 (也称为*参数*化) 定义参数, 你可以根据不同的部署需求重复且一致地部署逻辑应用。

例如, 如果部署到开发、测试和生产环境, 则可能对每个环境使用不同的连接字符串。 可以声明接受不同连接字符串的模板参数, 然后将这些字符串存储在单独的[参数文件](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)中。 这样, 就可以更改这些值, 而无需更新和重新部署模板。 对于具有敏感参数值或必须进行保护的参数值 (例如密码和机密) 的情况, 可以将这些值存储在[Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)中, 并让参数文件检索这些值。 但是, 在这些情况下, 你将重新部署以检索当前值。

本概述介绍了包含逻辑应用工作流定义的资源管理器模板中的属性。 模板和工作流定义都使用 JSON 语法, 但是存在一些差异, 因为工作流定义也遵循[工作流定义语言架构](../logic-apps/logic-apps-workflow-definition-language.md)。 例如, 模板表达式和工作流定义表达式在[引用参数](#parameter-references)的方式与它们可以接受的值之间有所不同。

> [!TIP]
> 若要获取最适合部署的有效参数化逻辑应用模板, 请使用 Visual Studio (免费社区版或更高版本) 以及适用于 Visual Studio 的 Azure 逻辑应用工具。 然后, 你可以[在 Visual studio 中创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), 或者在[visual studio 中查找并下载现有的逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。
>
> 或者, 你可以通过[将 Azure PowerShell 与 LogicAppTemplate 模块一起](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)使用来创建逻辑应用模板。

本主题中的示例逻辑应用使用[Office 365 Outlook 触发器](/connectors/office365/), 该触发器在新电子邮件到达时触发, 并使用[azure Blob 存储操作](/connectors/azureblob/)创建电子邮件正文的 blob, 并将该 Blob 上传到 Azure 存储容器。 这些示例还演示如何参数化不同于部署的值。

有关资源管理器模板的详细信息, 请参阅以下主题:

* [Azure 资源管理器模板结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure 资源管理器模板最佳做法](../azure-resource-manager/template-best-practices.md)
* [开发用于实现云一致性的 Azure 资源管理器模板](../azure-resource-manager/templates-cloud-consistency.md)

有关示例逻辑应用模板, 请参阅以下示例:

* 本主题示例使用的[完整模板](#full-example-template)
* GitHub 中的[示例快速入门逻辑应用模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create)

有关特定于逻辑应用、集成帐户和集成帐户项目的模板资源信息, 请参阅[Microsoft. 逻辑资源类型](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)。

<a name="template-structure"></a>

## <a name="template-structure"></a>模板结构

在顶层, 资源管理器模板遵循此结构, 此结构在[Azure 资源管理器模板结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)主题中进行了全面介绍:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

对于逻辑应用模板, 你主要使用以下模板对象:

| 特性 | 描述 |
|-----------|-------------|
| `parameters` | 声明用于接受在 Azure 中创建和自定义要部署的资源时要使用的值的[模板参数](../azure-resource-manager/resource-group-authoring-templates.md#parameters)。 例如, 这些参数接受逻辑应用的名称和位置、连接以及部署所需的其他资源的值。 您可以将这些参数值存储在[参数文件](#template-parameter-files)中, 本主题稍后将对此进行介绍。 有关常规详细信息, 请参阅[参数-资源管理器模板结构和语法](../azure-resource-manager/resource-group-authoring-templates.md#parameters)。 |
| `resources` | 定义要创建或更新的[资源](../azure-resource-manager/resource-group-authoring-templates.md#resources), 并将其部署到 azure 资源组, 例如逻辑应用、连接、Azure 存储帐户等。 有关常规详细信息, 请参阅[资源-资源管理器模板结构和语法](../azure-resource-manager/resource-group-authoring-templates.md#resources)。 |
||||

逻辑应用模板使用此文件名格式:

**<*逻辑应用名称*> json**

> [!IMPORTANT]
> 模板语法区分大小写, 因此请确保使用一致的大小写。 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>模板参数

逻辑应用模板具有多个`parameters`不同级别的对象, 并执行不同的功能。 例如, 在顶级, 你可以在 Azure 中创建和部署资源时, 声明值的[模板参数](../azure-resource-manager/resource-group-authoring-templates.md#parameters)以接受并在部署时使用:

* 逻辑应用
* 逻辑用于通过[托管连接器](../connectors/apis-list.md)访问其他服务和系统的连接
* 逻辑应用部署所需的其他资源

  例如, 如果逻辑应用使用企业到企业 (B2B) 方案的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), 则模板的顶级`parameters`对象将声明接受该集成帐户的资源 ID 的参数。

下面是参数定义的一般结构和语法, 它是通过[参数资源管理器模板结构和语法](../azure-resource-manager/resource-group-authoring-templates.md#parameters)进行了完全描述的:

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

此示例只显示用于在 Azure 中创建和部署这些资源的值的模板参数:

* 逻辑应用的名称和位置
* 要用于链接到逻辑应用的集成帐户的 ID

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

除了处理敏感值或必须保护的值的参数 (如用户名、密码和机密), 所有这些参数都包括`defaultValue`属性, 但在某些情况下, 默认值为空值。 用于这些模板参数的部署值由本主题后面介绍的示例[参数文件](#template-parameter-files)提供。

若要保护模板参数, 请参阅以下主题:

* [模板参数的安全建议](../azure-resource-manager/template-best-practices.md#parameters)
* [安全模板参数](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [通过 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)

其他模板对象通常引用模板参数, 以便它们可以使用通过模板参数传递的值, 例如:

* [模板的资源对象](#template-resources)(如本主题后面所述) 定义要创建和部署的 Azure 中的每个资源, 如[逻辑应用的资源定义](#logic-app-resource-definition)。 这些资源通常使用模板参数值, 例如逻辑应用的名称、位置和连接信息。

* 在逻辑应用的资源定义中,[工作流定义的参数对象](#workflow-definition-parameters)会在逻辑应用的运行时中声明要使用的值的参数。 例如, 可以声明 HTTP 触发器用于身份验证的用户名和密码的工作流定义参数。 若要为工作流定义参数指定值, 请`parameters`使用工作流定义*之外*但仍在逻辑应用的资源定义*中*的对象。 在此外部`parameters`对象中, 可以引用之前声明的模板参数, 这些参数可以在从参数文件部署时接受值。

引用参数时, 模板表达式和函数使用不同的语法, 并与工作流定义表达式和函数的行为不同。 有关这些差异的详细信息, 请参阅本主题后面的对[参数的引用](#parameter-references)。

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>最佳做法-模板参数

下面是一些用于定义参数的最佳方案:

* 根据你的部署需求, 只声明不同的值的参数。 不要为在不同部署要求中保持不变的值声明参数。

* 对于除了敏感或必须保护的值之外的所有参数, 请包括属性,该属性可以指定空值。`defaultValue` 始终为用户名、密码和机密使用安全参数。 若要隐藏或保护敏感的参数值, 请按照以下主题中的指导进行操作:

  * [模板参数的安全建议](../azure-resource-manager/template-best-practices.md#parameters)

  * [安全模板参数](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [通过 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)

* 若要区分工作流定义参数名称中的模板参数名称, 可以使用描述性模板参数名称, 例如:`TemplateFabrikamPassword`

有关模板最佳实践的详细信息, 请参阅[模板参数的最佳实践](../azure-resource-manager/template-best-practices.md#parameters)。

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>模板参数文件

若要提供模板参数的值, 请将这些值存储在[参数文件](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)中。 这样, 你就可以根据你的部署需求来使用不同的参数文件。 下面是要使用的文件名格式:

* 逻辑应用模板文件名:  **<*逻辑应用名称*> json**
* 参数文件名:  **<*逻辑应用名称*>**

下面是参数文件中的结构, 其中包括一个用于将[安全参数值与 Azure Key Vault 传递](../azure-resource-manager/resource-manager-keyvault-parameter.md)的密钥保管库参考:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

此示例参数文件指定之前在本主题中声明的模板参数的值:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>模板资源

模板包含一个`resources`对象, 该对象是一个数组, 其中包含要在 Azure 中创建和部署的每个资源的定义, 例如[逻辑应用的资源定义](#logic-app-resource-definition)、任何[连接资源定义](#connection-resource-definitions)以及任何其他资源逻辑应用需要部署。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> 模板可包含多个逻辑应用的资源定义, 因此请确保所有逻辑应用资源都指定相同的 Azure 资源组。 使用 Visual Studio 将模板部署到 Azure 资源组时, 系统会提示输入要打开的逻辑应用。 此外, Azure 资源组项目可以包含多个模板, 因此请确保在出现提示时选择正确的参数文件。

有关模板资源及其属性的常规信息, 请参阅以下主题:

* [资源-资源管理器模板结构和语法](../azure-resource-manager/resource-group-authoring-templates.md#resources)
* [模板资源的最佳实践](../azure-resource-manager/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>逻辑应用资源定义

逻辑应用的资源定义从`properties`对象开始, 其中包含以下信息:

* 逻辑应用在部署时的状态
* 逻辑应用使用的任何集成帐户的 ID
* 逻辑应用的工作流定义
* 一个`parameters`对象, 该对象设置要在运行时使用的值
* 有关逻辑应用的其他资源信息, 如名称、类型、位置等

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

下面是特定于逻辑应用资源定义的属性:

| 特性 | 必填 | 类型 | 描述 |
|-----------|----------|------|-------------|
| `state` | 是 | String | 逻辑应用在部署`Enabled`时的状态, 表示逻辑应用处于活动状态, `Disabled`这意味着逻辑应用处于非活动状态。 例如, 如果你尚未准备好逻辑应用, 但想要部署草稿版, 则可以使用`Disabled`选项。 |
| `integrationAccount` | 否 | Object | 如果逻辑应用使用集成帐户 (用于存储企业到企业 (B2B) 方案的项目), 则此对象包括`id`属性, 该属性指定集成帐户的 ID。 |
| `definition` | 是 | Object | 逻辑应用的基础工作流定义与代码视图中显示的对象相同, 并在[工作流定义语言的架构参考](../logic-apps/logic-apps-workflow-definition-language.md)主题中完全说明。 在此工作流定义中`parameters` , 对象声明要在逻辑应用运行时使用的值的参数。 有关详细信息, 请参阅[工作流定义和参数](#workflow-definition-parameters)。 <p><p>若要查看逻辑应用的工作流定义中的属性, 请从 "设计视图" 切换到 Azure 门户或 Visual Studio 中的 "代码视图", 或使用[Azure 资源浏览器](http://resources.azure.com)之类的工具。 |
| `parameters` | 否 | Object | 要在逻辑应用运行时中使用的[工作流定义参数值](#workflow-definition-parameters)。 这些值的参数定义显示在[工作流定义的 parameters 对象](#workflow-definition-parameters)内。 此外, 如果逻辑应用使用[托管连接器](../connectors/apis-list.md)来访问其他服务和系统, 则此对象将包含`$connections`一个对象, 该对象将设置要在运行时使用的连接值。 |
| `accessControl` | 否 | Object | 用于指定逻辑应用的安全属性, 如限制对请求触发器的 IP 访问, 或运行历史记录输入和输出。 有关详细信息, 请参阅[对逻辑应用的安全访问](../logic-apps/logic-apps-securing-a-logic-app.md)。 |
||||

有关特定于逻辑应用、集成帐户和集成帐户项目的模板资源信息, 请参阅[Microsoft. 逻辑资源类型](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)。

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>工作流定义和参数

逻辑应用的工作流定义显示在`definition`对象中, 该对象显示在逻辑应用的资源定义中的`properties`对象上。 此`definition`对象与代码视图中显示的对象相同, 并在[工作流定义语言的架构参考](../logic-apps/logic-apps-workflow-definition-language.md)主题中进行了完全说明。 您的工作流定义包含`parameters`内部声明对象, 您可以在其中为运行时工作流定义所使用的值定义新参数或编辑现有参数。 然后, 可以在工作流中的触发器或操作内引用这些参数。 默认情况下, `parameters`此对象为空, 除非你的逻辑应用通过[托管连接器](../connectors/apis-list.md)创建与其他服务和系统的连接。

若要设置工作流定义参数的值, 请`parameters`使用工作流定义*之外*但仍在逻辑应用的资源定义*中*的对象。 在此外部`parameters`对象中, 可以引用之前声明的模板参数, 这些参数可以在从参数文件部署时接受值。

> [!TIP]
>
> 最佳做法是, 不要直接引用部署时在工作流定义中评估的模板参数。 相反, 请声明一个工作流定义参数, 然后, 可以在工作`parameters`流定义*之外*的对象中设置该参数,*但仍可*在逻辑应用的资源定义中进行设置。 有关详细信息, 请参阅对[参数的引用](#parameter-references)。

此语法显示了可在模板和工作流定义级别同时声明参数的位置, 以及可通过引用模板和工作流定义参数设置这些参数值的位置:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": "[parameters('<template-parameter-name>')]"
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>安全工作流定义参数

对于在运行时处理敏感信息、密码、访问密钥或机密的工作流定义参数, 请声明或编辑参数以使用`securestring`或`secureobject`参数类型。 可以在工作流定义中的整个和内引用此参数。 在模板的顶级, 声明一个具有相同类型的参数, 以便在部署时处理此信息。

若要设置工作流定义参数的值, 请使用`parameters`工作流定义*之外*但仍在逻辑应用资源定义*中*的对象来引用模板参数。 最后, 若要在部署时将值传递给模板参数, 请将该值存储在[Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)中, 并在部署过程中模板使用的[参数文件](#template-parameter-files)中引用该密钥保管库。

此示例模板演示如何通过在必要时定义受保护的参数来完成这些任务, 以便可以将其值存储在 Azure Key Vault 中:

* 为用于对访问进行身份验证的值声明安全参数。
* 在模板和工作流定义级别均使用这些值。
* 使用参数文件提供这些值。

**模板**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**参数文件**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>最佳做法-工作流定义参数

若要确保逻辑应用设计器可以正确地显示工作流定义参数, 请遵循以下最佳做法:

* 对于除了敏感或必须保护的值之外的所有参数, 请包括属性,该属性可以指定空值。`defaultValue`

* 始终为用户名、密码和机密使用安全参数。 若要隐藏或保护敏感的参数值, 请按照以下主题中的指导进行操作:

  * [操作参数的安全建议](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [工作流定义中参数的安全建议](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [通过 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)

有关工作流定义参数的详细信息, 请参阅[参数-工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md#parameters)。

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>连接资源定义

当逻辑应用通过[托管连接器](../connectors/apis-list.md)创建和使用与其他服务和系统的连接时, 模板的`resources`对象将包含这些连接的资源定义。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

连接资源定义为其值引用模板的顶级参数, 这意味着可以使用参数文件在部署时提供这些值。 请确保连接与逻辑应用使用相同的 Azure 资源组和位置。

下面是 Office 365 Outlook 连接的示例资源定义以及相应的模板参数:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

逻辑应用的资源定义也可通过以下方式与连接资源定义一起使用:

* 在工作流定义中, `parameters`对象声明一个`$connections`用于在逻辑应用运行时中使用的连接值的参数。 此外, 创建连接的触发器或操作使用通过此`$connections`参数传递的相应值。

* 在工作流定义*之外*但仍在逻辑应用的资源定义*中*, `parameters`另一个对象通过引用相应的模板参数`$connections`来设置要在运行时为参数使用的值。 这些值使用模板表达式来引用资源, 这些资源安全地将连接的元数据存储在逻辑应用中。

  例如, 元数据可以包含连接字符串和访问令牌, 你可以将其存储在[Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)中。 若要将这些值传递给模板参数, 请在部署时在模板使用的[参数文件](#template-parameter-files)中引用该密钥保管库。 有关引用参数之间的差异的详细信息, 请参阅本主题后面的对[参数的引用](#parameter-references)。

  通过 Azure 门户或 Visual Studio 在代码视图中打开逻辑应用的工作流定义时, 该`$connections`对象会出现在工作流定义之外但处于同一级别。 在手动更新工作流定义时, 代码视图中的这种排序使这些参数更易于引用:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* 逻辑应用的资源定义包含一个`dependsOn`对象, 该对象指定逻辑应用使用的连接上的依赖关系。

在 Azure 中创建的每个连接都具有唯一的名称。 当你创建到同一个服务或系统的多个连接时, 每个连接名称将追加一个数字, 这会递增每个创建的新`office365`连接`office365-1`, 例如、、等。

此示例显示了逻辑应用的资源定义与 Office 365 Outlook 的连接资源定义之间的交互:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>安全连接参数

对于处理敏感信息、密码、访问密钥或机密的连接参数, 连接的资源定义包含一个`parameterValues`对象, 该对象以名称-值对格式指定这些值。 若要隐藏此信息, 可以使用`securestring`或`secureobject`参数类型声明或编辑这些值的模板参数。 然后, 你可以将该信息存储在[Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)中。 若要将这些值传递给模板参数, 请在部署时在模板使用的[参数文件](#template-parameter-files)中引用该密钥保管库。

下面是一个示例, 提供 Azure Blob 存储连接的帐户名称和访问密钥:

**参数文件**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**模板**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>验证连接

部署完成后, 逻辑应用使用有效的参数以端到端的方式工作。 但是, 你仍必须对任何 OAuth 连接授权以生成有效的访问令牌, 以便对[凭据进行身份验证](../active-directory/develop/authentication-scenarios.md)。 有关详细信息, 请参阅[授权 OAuth 连接](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)。

某些连接支持使用 Azure Active Directory (Azure AD)[服务主体](../active-directory/develop/app-objects-and-service-principals.md)来授权[Azure AD 中注册](../active-directory/develop/quickstart-register-app.md)的逻辑应用的连接。 例如, 下面 Azure Data Lake 连接资源定义显示了如何引用处理服务主体信息的模板参数, 以及模板如何声明这些参数:

**连接资源定义**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| 特性 | 描述 |
|-----------|-------------|
| `token:clientId` | 与服务主体关联的应用程序或客户端 ID |
| `token:clientSecret` | 与服务主体关联的键值 |
| `token:TenantId` | Azure AD 租户的目录 ID |
| `token:grantType` | 请求的授予类型, 该类型必须`client_credentials`为。 有关详细信息, 请参阅[Microsoft 标识平台和 OAuth 2.0 客户端凭据流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 |
|||

**模板参数定义**

模板的顶级`parameters`对象为示例连接声明这些参数:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

有关使用服务主体的详细信息, 请参阅以下主题:

* [使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)
* [使用 Azure PowerShell 创建 Azure 服务主体](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [使用 Azure PowerShell 创建具有证书的服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>对参数的引用

若要引用模板参数, 可以将模板表达式用于在部署时计算的[模板函数](../azure-resource-manager/resource-group-template-functions.md)。 模板表达式使用方括号 ( **[]** ):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

若要引用工作流定义参数, 请使用[工作流定义语言表达式和函数](../logic-apps/workflow-definition-language-functions-reference.md)(在运行时计算)。 你可能会注意到某些模板函数和工作流定义函数具有相同的名称。 工作流定义表达式以 "at" 符号 ( **@** ) 开头:

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

可以将模板参数值传递给逻辑应用的工作流定义, 以便在运行时使用。 但是, 请避免在工作流定义中使用模板参数、表达式和语法, 因为逻辑应用设计器不支持模板元素。 此外, 由于在计算表达式时的差异, 模板语法和表达式可能会使代码变得复杂。

相反, 请按照以下常规步骤声明和引用要在运行时使用的工作流定义参数, 声明并引用要在部署时使用的模板参数, 并指定在部署时通过使用参数文件传入的值。 有关完整详细信息, 请参阅本主题前面的[工作流定义和参数](#workflow-definition-parameters)部分。

1. 创建模板, 并为要在部署时接受和使用的值声明模板参数。

1. 在工作流定义中, 为要在运行时接受并使用的值声明参数。 然后, 你可以在工作流定义中整个和引用这些值。

1. 在工作流定义*之外* *但仍在*逻辑应用的资源定义中的对象中,通过引用相应的模板参数来设置工作流定义参数的值。`parameters` 这样, 便可以将模板参数值传递到工作流定义参数。

1. 在参数文件中, 指定模板的值, 以便在部署时使用。

<a name="full-example-template"></a>

## <a name="full-example-template"></a>完整示例模板

下面是本主题示例使用的参数化示例模板:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建逻辑应用模板](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)