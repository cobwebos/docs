---
title: 创建用于保存值的变量 - Azure 逻辑应用 | Microsoft Docs
description: 如何在 Azure 逻辑应用中通过创建变量来保存和管理值
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 0efce9fbbbd241f335f08bb258b6ba343982fdb9
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299182"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建用于保存和管理值的变量

本文介绍如何通过创建变量，在整个逻辑应用中存储和使用值。 例如，变量可帮助统计某个循环运行的次数。 循环访问某个数组或检查特定项的数组时，可以使用变量来引用每个数组项的索引号。 

可为整数、浮点数、布尔值、字符串、数组和对象等数据类型创建变量。 创建变量后，可以执行其他任务，例如：

* 获取或引用变量的值。
* 按常量值增大或减小变量，也称为递增和递减。
* 将不同的值赋给变量。
* 插入或追加变量值作为字符串或数组中的最后一个时间。

变量只在创建它们的逻辑应用实例中存在并保持全局性。 另外，它们保留在逻辑应用实例中的任何循环迭代之间。 引用变量时，请使用变量的名称作为令牌，而不要使用操作的名称。操作名称通常用于引用操作的输出。

如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

## <a name="prerequisites"></a>先决条件

若要遵循本文，需要准备好以下各项：

* 要在其中创建变量的逻辑应用 

  如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 用作逻辑应用中第一个步骤的[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  在添加用于创建和使用变量的操作之前，必须使用触发器启动逻辑应用。

<a name="create-variable"></a>

## <a name="initialize-variable"></a>初始化变量

可以创建一个变量并声明其数据类型和初始值 - 在逻辑应用中的一个操作内指定所有这些对象。 可以仅在全局级别声明变量，而不是在作用域、条件和循环中声明。 

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 

   本示例使用 Azure 门户，以及包含现有触发器的逻辑应用。

2. 在逻辑应用中要添加变量的步骤下，执行以下步骤之一： 

   * 若要在最后一个步骤下添加操作，请选择“新建步骤”>“添加操作”。

     ![添加操作](./media/logic-apps-create-variables-store-values/add-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上方，以显示加号 (+)。 
   选择加号，然后选择“添加操作”。

3. 在搜索框中，输入“变量”作为筛选器。 在操作列表中，选择“变量 - 初始化变量”。

   ![选择操作](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. 提供变量的以下信息：

   | 属性 | 必选 | 值 |  说明 |
   |----------|----------|-------|--------------|
   | 名称 | 是 | <*variable-name*> | 要递增的变量的名称 | 
   | Type | 是 | <*variable-type*> | 变量的数据类型 | 
   | 值 | 否 | <*start-value*> | 变量的初始值 <p><p>**提示**：尽管此值是可选的，但最好是设置此值，以便始终知道变量的起始值。 | 
   ||||| 

   ![初始化变量](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. 现在，继续添加所需的操作。 完成后，请在设计器工具栏上选择“保存”。

如果从设计器切换到代码视图编辑器，“初始化变量”操作会采用 JavaScript 对象表示法 (JSON) 格式，按以下方式显示在逻辑应用定义中：

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

下面是其他一些变量类型示例：

*字符串变量*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*布尔值变量*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*包含整数的数组*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*包含字符串的数组*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>获取变量的值

若要检索或引用变量的内容，也可以在逻辑应用设计器和代码视图编辑器中使用 [variables() 函数](../logic-apps/workflow-definition-language-functions-reference.md#variables)。
引用变量时，请使用变量的名称作为令牌，而不要使用操作的名称。操作名称通常用于引用操作的输出。 

例如，此表达式使用 **variables()** 函数，从[本文前面创建的](#append-value)数组变量中获取项。 **string()** 函数以字符串格式返回变量的内容：`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>递增变量 

若要按常量值增大或递增某个变量，请将“变量 - 递增变量”操作添加到逻辑应用。 此操作仅适用于整数和浮点数变量。

1. 在逻辑应用设计器中要增大现有变量的步骤下，选择“新建步骤” > “添加操作”。 

   例如，此逻辑应用包含一个触发器，以及一个创建了变量的操作。 因此，请在以下步骤下添加新操作：

   ![添加操作](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方，以显示加号 (+)。 选择加号，然后选择“添加操作”。

2. 在搜索框中，输入“递增变量”作为筛选器。 在操作列表中，选择“变量 - 递增变量”。

   ![选择“递增变量”操作](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. 提供用于递增变量的以下信息：

   | 属性 | 必选 | 值 |  说明 |
   |----------|----------|-------|--------------|
   | 名称 | 是 | <*variable-name*> | 要递增的变量的名称 | 
   | 值 | 否 | <*increment-value*> | 用于递增变量的值。 默认值为 1。 <p><p>**提示**：尽管此值是可选的，但最好是设置此值，以便始终知道用于递增变量的特定值。 | 
   |||| 

   例如： 
   
   ![递增值的示例](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. 完成后，请在设计器工具栏上选择“保存”。 

如果从设计器切换到代码视图编辑器，“递增变量”操作会采用 JSON 格式，按以下方式显示在逻辑应用定义中：

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>示例：创建循环计数器

变量通常用于统计某个循环的运行次数。 本示例通过创建一个统计电子邮件中附件数的循环，来演示如何创建和使用变量完成此任务。

1. 在 Azure 门户中，创建一个空白逻辑应用。 添加一个触发器，用于检查新电子邮件和任何附件。 

   本示例使用 Office 365 Outlook 触发器“收到新电子邮件时”。 
   可将此触发器设置为仅当电子邮件包含附件时才触发。
   但是，可以使用任何可检查包含附件的新电子邮件的连接器，例如 Outlook.com 连接器。

2. 在触发器中，选择“显示高级选项”。 若要让触发器检查附件并将这些附件传入逻辑应用的工作流，请为这些属性选择“是”：
   
   * **带有附件** 
   * **包括附件** 

   ![检查和包含附件](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. 添加[“初始化变量”操作](#create-variable)。 创建名为 **Count**、起始值为 0 的整数变量。

   ![为“初始化变量”添加操作](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. 若要循环访问每个附件，请选择“新建步骤” > “更多” > “添加 for each”添加 *for each* 循环。

   ![添加“for each”循环](./media/logic-apps-create-variables-store-values/add-loop.png)

5. 在该循环中，单击“从先前的步骤中选择一个输出”框。 当动态内容列表出现时，选择“附件”。 

   ![选择“附件”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   “附件”字段将包含触发器输出中电子邮件附件的数组传入循环。

6. 在“for each”循环中，选择“添加操作”。 

   ![选择“添加操作”。](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. 在搜索框中，输入“递增变量”作为筛选器。 在操作列表中，选择“变量 - 递增变量”。

   > [!NOTE]
   > 确保“递增变量”操作显示在循环内部。 如果该操作显示在循环外部，请将其拖到循环中。

8. 在“递增变量”操作中，从“名称”列表中选择“Count”变量。 

   ![选择“Count”变量](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. 在循环下，添加用于发送附件数目的任何操作。 在操作中，包含来自 **Count** 变量的值，例如： 

   ![添加用于发送结果的操作](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. 保存逻辑应用。 在设计器工具栏上，选择“保存”。 

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 如果尚未启用逻辑应用，请在逻辑应用菜单中选择“概述”。 在工具栏上选择“启用”。 

2. 在逻辑应用设计器工具栏上，选择“运行”。 此步骤会手动启动逻辑应用。

3. 将包含一个或多个附件的电子邮件发送到本示例中使用的电子邮件帐户。

   此步骤会触发逻辑应用的触发器，而该触发器会创建并运行逻辑应用工作流的实例。
   因此，逻辑应用会发送消息或电子邮件，其中显示了发送的电子邮件中的附件数目。

如果从设计器切换到代码视图编辑器，“for each”循环与“递增变量”操作会采用 JSON 格式，按以下方式显示在逻辑应用定义中。

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>递减变量

若要按常量值减小或递减变量，请遵循[增大变量](#increment-value)的步骤，不过，需要找到并选择“变量 - 递减变量”操作。 此操作仅适用于整数和浮点数变量。

下面是“递减变量”操作的属性：

| 属性 | 必选 | 值 |  说明 |
|----------|----------|-------|--------------|
| 名称 | 是 | <*variable-name*> | 要递减的变量的名称 | 
| 值 | 否 | <*increment-value*> | 用于递减变量的值。 默认值为 1。 <p><p>**提示**：尽管此值是可选的，但最好是设置此值，以便始终知道用于递减变量的特定值。 | 
||||| 

如果从设计器切换到代码视图编辑器，“递减变量”操作会采用 JSON 格式，按以下方式显示在逻辑应用定义中。

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>设置变量

若要将不同的值赋给现有变量，请遵循[增大变量](#increment-value)的步骤，不过需要： 

1. 找到并选择“变量 - 设置变量”操作。 

2. 提供变量名称，以及要赋的值。 新值和变量的数据类型必须相同。
之所以需要该值，是因为此操作没有默认值。 

下面是“设置变量”操作的属性：

| 属性 | 必选 | 值 |  说明 | 
|----------|----------|-------|--------------| 
| 名称 | 是 | <*variable-name*> | 要更改的变量的名称 | 
| 值 | 是 | <*new-value*> | 要赋给变量的值。 两者的数据类型必须相同。 | 
||||| 

> [!NOTE]
> 除非递增或递减变量，否则更改循环中的变量可能会产生意外的结果，因为循环在默认情况下是并行或并发运行的。 对于这种情况，请尝试将循环设置为按顺序运行。 例如，若要引用循环中的变量值，并且预期该循环实例开头和末尾的值相同，请遵循以下步骤更改循环的运行方式： 
>
> 1. 在循环的右上角选择省略号 (...) 按钮，然后选择“设置”。
> 
> 2. 在“并发控制”下，将“替代默认值”设置更改为“打开”。
>
> 3. 将“并行度”滑块拖到 **1**。

如果从设计器切换到代码视图编辑器，“设置变量”操作会采用 JSON 格式，按以下方式显示在逻辑应用定义中。 本示例将“Count”变量的当前值更改为另一个值。 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>追加到变量

对于存储字符串或数组的变量，可以插入或追加一个变量值，作为这些字符串或数组中的最后一个项。 可以遵循[增大变量](#increment-value)的步骤，不过，需要做出以下变化： 

1. 根据变量是字符串还是数组，找到并选择以下操作之一： 

  * **变量 - 追加到字符串变量**
  * **变量 - 追加到数组变量** 

2. 提供要追加为字符串或数组中最后一个项的值。 此值是必需的。 

下面是“追加到...”操作的属性：

| 属性 | 必选 | 值 |  说明 | 
|----------|----------|-------|--------------| 
| 名称 | 是 | <*variable-name*> | 要更改的变量的名称 | 
| 值 | 是 | <*append-value*> | 要追加的值，可以是任何类型 | 
|||||  

如果从设计器切换到代码视图编辑器，“追加到数组变量”操作会采用 JSON 格式，按以下方式显示在逻辑应用定义中。
本示例创建一个数组变量，并将另一个值添加为数组中的最后一个项。 结果是包含此数组的已更新变量：`[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解[逻辑应用连接器](../connectors/apis-list.md)
