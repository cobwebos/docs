---
title: "使用 Liquid 转换转换 JSON 数据 - Azure 逻辑应用 | Microsoft Docs"
description: "使用逻辑应用和 Liquid 模板创建高级 JSON 转换的转换或映射。"
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: c1a1a5530c19d39a8e37d122235c8340caa88570
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2018
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>使用 Liquid 模板执行高级 JSON 转换

Azure 逻辑应用支持通过撰写或分析 JSON 等本机数据操作完成基本 JSON 转换。 对于高级 JSON 转换，可将逻辑应用与 Liquid 模板配合使用。 
[Liquid](https://shopify.github.io/liquid/) 是一种用于灵活的 Web 应用的开放源代码模板语言。
 
本文介绍如何使用 Liquid 映射或模板，它们支持较复杂的 JSON 转换，如迭代、控制流、变量等。 在逻辑应用中执行 Liquid 转换之前，必须先使用 Liquid 映射定义从 JSON 到 JSON 的映射，然后将该映射存储在集成帐户中。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 也可以[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 基本[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>为集成帐户创建 Liquid 模板或映射

1. 创建此示例的示例 Liquid 模板。 Liquid 模板定义如何转换 JSON 输入，如下所述：

   ``` json
   {%- assign deviceList = content.devices | Split: ', ' -%}
      {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
      }
   ```
   > [!NOTE]
   > 如果 Liquid 模板使用任何[筛选器](https://shopify.github.io/liquid/basics/introduction/#filters)，则这些筛选器必须以大写开头。 

2. 登录到 [Azure 门户](https://portal.azure.com)。

3. 在 Azure 主菜单上，选择“所有资源”。 

4. 在搜索框中，查找并选择你的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  在“集成帐户”磁贴中，选择“映射”。

   ![选择映射](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. 选择“添加”，并为映射提供以下详细信息：

   * **名称**：映射的名称，此示例中为“JsontoJsonTemplate”
   * **映射类型**：映射的类型。 对于 JSON 到 JSON 转换，必须选择“liquid”。
   * **映射**：用于转换的现有 Liquid 模板或映射文件，此示例中为“SimpleJsonToJsonTemplate.liquid”。 若要查找此文件，可使用文件选取器。

   ![添加 liquid 模板](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>为 JSON 转换添加 Liquid 操作

1. [创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

2. 将[请求触发器](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)添加到逻辑应用。

3. 选择“+ 新建步骤”>“添加操作”。 在搜索框中，输入“liquid”，然后选择“Liquid - 将 JSON 转换为 JSON”。

   ![查找并选择 Liquid 操作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. 在“内容”框中，从动态内容列表或参数列表（以显示为准）中选择“正文”。
  
   ![选择正文](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. 从“映射”列表中选择 Liquid 模板，此示例中为“JsonToJsonTemplate”。

   ![选择映射](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果列表为空，逻辑应用可能未链接到集成帐户。 
   要将逻辑应用链接到具有 Liquid 模板或映射的集成帐户，请执行以下步骤：

   1. 在逻辑应用菜单中选择“工作流设置”。
   2. 从“选择集成帐户”列表中，选择集成帐户，并选择“保存”。

   ![将逻辑应用链接到集成帐户](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>测试逻辑应用

通过 [Postman](https://www.getpostman.com/postman) 或类似工具，将 JSON 输入发布到逻辑应用。 逻辑应用转换后的 JSON 输出如此示例中所示：
  
![示例输出](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)

## <a name="next-steps"></a>后续步骤

* [了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  
* [了解有关映射的详细信息](../logic-apps/logic-apps-enterprise-integration-maps.md "了解企业集成映射")  

