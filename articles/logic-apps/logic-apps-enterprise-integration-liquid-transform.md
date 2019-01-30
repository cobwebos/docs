---
title: 使用 Liquid 转换转换 JSON 数据 - Azure 逻辑应用 | Microsoft Docs
description: 使用逻辑应用和 Liquid 模板创建用于高级 JSON 转换的转换或映射
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
manager: jeconnoc
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: d607c75bc451774e6bf269eb658236d93a85021f
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54854371"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用 Liquid 模板执行高级 JSON 转换

可以在逻辑应用中通过**撰写**或**分析 JSON** 等本机数据操作完成基本 JSON 转换。 若要执行高级 JSON 转换，可以使用 [Liquid](https://shopify.github.io/liquid/)（一种用于灵活 Web 应用的开源模板语言）创建模板或映射。 Liquid 模板允许你定义如何转换 JSON 输出并支持复杂的 JSON 转换，例如迭代、控制流、变量，等等。 

因此，在可以在逻辑应用中执行 Liquid 转换之前，必须先使用 Liquid 映射定义从 JSON 到 JSON 的映射，然后将该映射存储在集成帐户中。 本文展示了如何创建并使用此 Liquid 模板或映射。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 或者[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 基本[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 关于 [Liquid 模板语言](https://shopify.github.io/liquid/)的基本知识。

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>为集成帐户创建 Liquid 模板或映射

1. 对于此示例，请创建此步骤中所述的示例 Liquid 模板。
如果希望在 Liquid 模板中使用任何筛选器，请确保这些筛选器以大写字母开头。 了解有关 [Liquid 筛选器](https://shopify.github.io/liquid/basics/introduction/#filters)的更多信息，此类筛选器使用 [DotLiquid](https://dotliquidmarkup.org/) 和 C# 命名约定。

   ```json
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

2. 登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 主菜单中，选择“所有资源”。 在搜索框中，查找并选择你的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  在“组件”下，选择“映射”。

    ![选择映射](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. 选择“添加”，并为映射提供以下详细信息：

   | 属性 | 值 | 说明 | 
   |----------|-------|-------------|
   | **名称** | JsonToJsonTemplate | 映射的名称，在此示例中为“JsonToJsonTemplate” | 
   | **映射类型** | **liquid** | 你的映射的类型。 对于 JSON 到 JSON 转换，必须选择“liquid”。 | 
   | **Map** | "SimpleJsonToJsonTemplate.liquid" | 用于转换的现有 Liquid 模板或映射文件，在此示例中为“SimpleJsonToJsonTemplate.liquid”。 若要查找此文件，可使用文件选取器。 |
   ||| 

   ![添加 liquid 模板](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>为 JSON 转换添加 Liquid 操作

1. 在 Azure 门户中，执行以下步骤[创建空的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

2. 在逻辑应用设计器中，向逻辑应用中添加[请求触发器](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)。

3. 在触发器下，选择“新建步骤”。 在搜索框中，输入“liquid”作为筛选器，然后选择以下操作：**将 JSON 转换为 JSON - Liquid**

   ![查找并选择 Liquid 操作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. 在“内容”框中单击以显示动态内容列表，并选择“正文”标记。
  
   ![选择正文](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. 从“映射”列表中选择 Liquid 模板，此示例中为“JsonToJsonTemplate”。

   ![选择映射](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果映射列表为空，则逻辑应用很可能未链接到集成帐户。 
   要将逻辑应用链接到具有 Liquid 模板或映射的集成帐户，请执行以下步骤：

   1. 在逻辑应用菜单中选择“工作流设置”。

   2. 从“选择集成帐户”列表中，选择集成帐户，并选择“保存”。

     ![将逻辑应用链接到集成帐户](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>测试逻辑应用

通过 [Postman](https://www.getpostman.com/postman) 或类似工具，将 JSON 输入发布到逻辑应用。 逻辑应用转换后的 JSON 输出如此示例中所示：
  
![示例输出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>更多 Liquid 操作示例
Liquid 并非仅可用于 JSON 转换。 下面列出了使用 Liquid 的其他可用转换操作。

* 将 JSON 转换为文本
  
  下面是用于此示例的 Liquid 模板：
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   下面是示例输入和输出：
  
   ![将 JSON 输出为文本的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* 将 XML 转换为 JSON
  
  下面是用于此示例的 Liquid 模板：
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   下面是示例输入和输出：

   ![将 XML 输出为 JSON 的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* 将 XML 转换为文本
  
  下面是用于此示例的 Liquid 模板：

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   下面是示例输入和输出：

   ![将 XML 输出为文本的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>后续步骤

* [了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  
* [了解有关映射的详细信息](../logic-apps/logic-apps-enterprise-integration-maps.md "了解企业集成映射")  

