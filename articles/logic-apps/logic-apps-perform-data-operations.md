---
title: 对数据执行操作-Azure 逻辑应用
description: 在 Azure 逻辑应用中转换、管理和操作数据输出与格式
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: 1b0a7473f1cdfb6aa3533b261979da7c18605a16
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179746"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>在 Azure 逻辑应用中执行数据操作

本文介绍如何通过添加如下所述任务的操作，来处理逻辑应用中的数据：

* 从数组创建表。
* 基于条件从其他数组创建数组。
* 从 JavaScript 对象表示法 (JSON) 对象属性创建用户友好的令牌，以便可以在工作流中轻松使用这些属性。

如果在此处找不到所需的操作，请尝试浏览 Azure 逻辑应用提供的许多不同的[数据操作函数](../logic-apps/workflow-definition-language-functions-reference.md)。

这些表格汇总了可用的数据操作，并根据操作可以处理的源数据类型进行组织，但每项说明以字母顺序显示。

**数组操作** 

这些操作帮助处理数组中的数据。

| 操作 | 描述 |
|--------|-------------|
| [**创建 CSV 表**](#create-csv-table-action) | 从数组创建逗号分隔值 (CSV) 表。 |
| [**创建 HTML 表**](#create-html-table-action) | 从数组创建 HTML 表。 |
| [**筛选数组**](#filter-array-action) | 基于指定的筛选器或条件从数组创建数组子集。 |
| [**联接**](#join-action) | 基于数组中的所有项创建一个字符串，并使用指定的字符分隔每个项。 |
| [**选择**](#select-action) | 从不同数组中所有项的指定属性创建一个数组。 |
||| 

**JSON 操作**

这些操作帮助处理采用 JavaScript 对象表示法 (JSON) 格式的数据。

| 操作 | 描述 |
|--------|-------------|
| [Compose](#compose-action) | 从可能具有不同数据类型的多个输入创建一个消息或字符串。 然后可以使用此字符串作为单个输入，而无需反复输入相同的输入。 例如，可以从各种输入创建单个 JSON 消息。 |
| [**分析 JSON**](#parse-json-action) | 为 JSON 内容中的属性创建用户友好的数据令牌，以便在逻辑应用中更轻松地使用这些属性。 |
|||

若要创建更复杂的 JSON 转换，请参阅[使用 Liquid 模板执行高级 JSON 转换](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 需在其中使用操作处理数据的逻辑应用

  如果你不熟悉逻辑应用，请参阅[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 用作逻辑应用中第一个步骤的[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  数据操作只以操作的形式提供，因此，在使用这些操作之前，请使用触发器启动逻辑应用，并包含用于创建所需输出的其他所有操作。

<a name="compose-action"></a>

## <a name="compose-action"></a>Compose 操作

若要构造单个输出（例如多个输入中的 JSON 对象），可以使用 "**撰写**" 操作。 输入可以采用不同的类型，例如整数、布尔值、数组、JSON 对象，以及 Azure 逻辑应用支持的其他任何本机类型（如二进制和 XML）。 然后，可以在“撰写”操作后面的操作中使用输出。 “撰写”操作还能避免在生成逻辑应用的工作流时反复输入相同的输入。

举例而言，可以从多个变量（例如，存储名字和姓氏的字符串变量，以及存储年龄的整数变量）构造 JSON 消息。 此处的“撰写”操作接受以下输入：

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

并创建此输出：

`{"age":35,"fullName":"Owens,Sophie"}`

若要尝试示例，请在逻辑应用设计器中遵循以下步骤。 或者，如果你喜欢在代码视图编辑器中工作，则可以将本文中的示例**组合**和**初始化变量**操作定义复制到自己的逻辑应用的基础工作流定义中：[数据操作代码示例-撰写](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。

   本示例使用 Azure 门户以及包含一个“重复”触发器和多个“初始化变量”操作的逻辑应用。 这些操作设置用于创建两个字符串变量和一个整数变量。 稍后测试逻辑应用时，可以手动运行应用，而无需等待触发器激发。

   ![启动示例逻辑应用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. 在要创建输出的逻辑应用中，执行以下步骤之一： 

   * 要在上一步中添加操作，请选择 "**新建步骤**"。

     ![添加操作](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上，以便显示 **+** 加号（）。 选择加号，然后选择 "**添加操作**"。

1. 在 "**选择操作**" 下的 "搜索" 框`compose`中，输入作为筛选器。 从 "操作" 列表中，选择 "**撰写**" 操作。

   ![选择“撰写”操作](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. 在“输入”框中，提供用于创建输出的输入。

   对于本示例，在“输入”框中单击时，会显示动态内容列表，以便可以选择先前创建的变量：

   ![选择用于撰写的输入](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   下面是已完成的示例“撰写”操作： 

   ![完成的“撰写”操作](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

有关基础工作流定义中此操作的详细信息，请参阅[“撰写”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)。

### <a name="test-your-logic-app"></a>测试逻辑应用

若要确认“撰写”操作是否创建了预期的结果，请向自己发送一条通知，其中包含“撰写”操作的输出。

1. 在逻辑应用中，添加一个可以向自己发送“撰写”操作结果的操作。

1. 在该操作中，单击希望显示结果的任意位置。 当动态内容列表打开时，请在“撰写”操作下面选择“输出”。

   此示例使用 "**发送电子邮件**" 操作，并在电子邮件的正文和主题中包含**输出**字段：

   ![“发送电子邮件”操作中的“输出”字段](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. 现在，请手动运行逻辑应用。 在设计器工具栏上选择“运行”。

   根据所用的电子邮件连接器，会收到下面所示的结果：

   ![包含“撰写”操作结果的电子邮件](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>“创建 CSV 表”操作

若要创建一个逗号分隔值（CSV）表，该表具有数组中 JavaScript 对象表示法（JSON）对象的属性和值，请使用 "**创建 CSV 表**" 操作。 然后，可以在“创建 CSV 表”操作后面的操作中使用生成的表。

如果你更喜欢在 "代码视图" 编辑器中工作，则可以将此示例中的 "**创建 CSV 表**" 和 "**初始化变量**操作定义" 从本文复制到你自己的逻辑应用的基础工作流定义：[数据操作代码示例-创建 CSV 表](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。

   本示例使用 Azure 门户以及包含一个“重复”触发器和一个“初始化变量”操作的逻辑应用。 该操作设置用于创建一个变量，该变量的初始值是包含一些采用 JSON 格式的属性和值的数组。 稍后测试逻辑应用时，可以手动运行应用，而无需等待触发器激发。

   ![启动示例逻辑应用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. 在要创建 CSV 表的逻辑应用中，执行以下步骤之一： 

   * 要在上一步中添加操作，请选择 "**新建步骤**"。

     ![添加操作](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上，以便显示 **+** 加号（）。 选择加号，然后选择 "**添加操作**"。

1. 在 "**选择操作**" 下的 "搜索" 框`create csv table`中，输入作为筛选器。 从 "操作" 列表中，选择 "**创建 CSV 表**" 操作。

   ![选择“创建 CSV 表”操作](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. 在“从”框中，提供用于创建表的数组或表达式。

   对于本示例，在“从”框中单击时，会显示动态内容列表，以便可以选择先前创建的变量：

   ![选择用于创建 CSV 表的数组输出](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > 若要为 JSON 对象中的属性创建用户友好的令牌，以便可以选择这些属性作为输入，请在调用“创建 CSV 表”操作之前使用[分析 JSON](#parse-json-action)。

   下面是已完成的示例“创建 CSV 表”操作： 

   ![完成的“创建 CSV 表”操作](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

### <a name="customize-table-format"></a>自定义表格式

默认情况下，"**列**" 属性被设置为基于数组项自动创建表列。 

若要指定自定义标头和值，请执行以下步骤：

1. 打开 "**列**" 列表，然后选择 "**自定义**"。

1. 在 "**标头**" 属性中，指定要改用的自定义标头文本。

1. 在 "**密钥**" 属性中，指定要改用的自定义值。

若要引用和编辑数组中的值，可以在 " `@item()` **创建 CSV 表**" 操作的 JSON 定义中使用函数。

1. 在设计器工具栏上，选择 "**代码视图**"。 

1. 在代码编辑器中，编辑操作的`inputs`部分，按所需的方式自定义表输出。

此示例通过`header`将属性设置为空值并取消引用每`columns`个`value`属性，来仅返回数组中的列值，而不返回标头：

```json
"Create_CSV_table": {
   "inputs": {
      "columns": [
         { 
            "header": "",
            "value": "@item()?['Description']"
         },
         { 
            "header": "",
            "value": "@item()?['Product_ID']"
         }
      ],
      "format": "CSV",
      "from": "@variables('myJSONArray')"
   }
}
```

下面是此示例返回的结果：

```text
Results from Create CSV table action:

Apples,1
Oranges,2
```

在设计器中，"**创建 CSV 表**" 操作现在以这种方式显示：

!["创建 CSV 表"，无列标题](./media/logic-apps-perform-data-operations/create-csv-table-no-column-headers.png)

有关基础工作流定义中此操作的详细信息，请参阅[“表”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)。

### <a name="test-your-logic-app"></a>测试逻辑应用

若要确认“创建 CSV 表”操作是否创建了预期的结果，请向自己发送一条通知，其中包含“创建 CSV 表”操作的输出。

1. 在逻辑应用中，添加一个可以向自己发送“创建 CSV 表”操作结果的操作。

1. 在该操作中，单击希望显示结果的任意位置。 当动态内容列表打开时，请在“创建 CSV 表”操作下面选择“输出”。 

   此示例使用 Office 365 Outlook 的 "**发送电子邮件**" 操作，并在电子邮件的正文中包含**输出**字段：

   ![“发送电子邮件”操作中的“输出”字段](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. 现在，请手动运行逻辑应用。 在设计器工具栏上选择“运行”。

   根据所用的电子邮件连接器，会收到下面所示的结果：

   ![包含“创建 CSV 表”操作结果的电子邮件](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>“创建 HTML 表”操作

若要创建具有数组中 JavaScript 对象表示法（JSON）对象属性和值的 HTML 表，请使用 "**创建 HTML 表**" 操作。 然后，可以在“创建 HTML 表”操作后面的操作中使用生成的表。

如果你更喜欢在代码视图编辑器中工作，则可以将此示例中的 "**创建 HTML 表**" 和 "**初始化变量**操作" 定义复制到自己的逻辑应用的基础工作流定义：[数据操作代码示例-创建 HTML 表](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。

   本示例使用 Azure 门户以及包含一个“重复”触发器和一个“初始化变量”操作的逻辑应用。 该操作设置用于创建一个变量，该变量的初始值是包含一些采用 JSON 格式的属性和值的数组。 稍后测试逻辑应用时，可以手动运行应用，而无需等待触发器激发。

   ![启动示例逻辑应用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. 在要创建 HTML 表的逻辑应用中，执行以下步骤之一：

   * 要在上一步中添加操作，请选择 "**新建步骤**"。

     ![添加操作](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上，以便显示 **+** 加号（）。 选择加号，然后选择 "**添加操作**"。

1. 在 "**选择操作**" 下的 "搜索" 框`create html table`中，输入作为筛选器。 从 "操作" 列表中，选择 "**创建 HTML 表**" 操作。

   ![选择“创建 HTML 表”操作](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. 在“从”框中，提供用于创建表的数组或表达式。

   对于本示例，在“从”框中单击时，会显示动态内容列表，以便可以选择先前创建的变量：

   ![选择用于创建 HTML 表的数组输出](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > 若要为 JSON 对象中的属性创建用户友好的令牌，以便可以选择这些属性作为输入，请在调用“创建 HTML 表”操作之前使用[分析 JSON](#parse-json-action)。

   下面是已完成的示例“创建 HTML 表”操作：

   ![完成的“创建 HTML 表”操作](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

### <a name="customize-table-format"></a>自定义表格式

默认情况下，"**列**" 属性被设置为基于数组项自动创建表列。 

若要指定自定义标头和值，请执行以下步骤：

1. 打开 "**列**" 列表，然后选择 "**自定义**"。

1. 在 "**标头**" 属性中，指定要改用的自定义标头文本。

1. 在 "**密钥**" 属性中，指定要改用的自定义值。

若要引用和编辑数组中的值，可以在 " `@item()` **创建 HTML 表**" 操作的 JSON 定义中使用函数。

1. 在设计器工具栏上，选择 "**代码视图**"。 

1. 在代码编辑器中，编辑操作的`inputs`部分，按所需的方式自定义表输出。

此示例通过`header`将属性设置为空值并取消引用每`columns`个`value`属性，来仅返回数组中的列值，而不返回标头：

```json
"Create_HTML_table": {
   "inputs": {
      "columns": [
         { 
            "header": "",
            "value": "@item()?['Description']"
         },
         { 
            "header": "",
            "value": "@item()?['Product_ID']"
         }
      ],
      "format": "HTML",
      "from": "@variables('myJSONArray')"
   }
}
```

下面是此示例返回的结果：

```text
Results from Create HTML table action:

Apples    1
Oranges   2
```

在设计器中，"**创建 HTML 表**" 操作现在以这种方式显示：

!["创建 HTML 表"，无列标题](./media/logic-apps-perform-data-operations/create-html-table-no-column-headers.png)

有关基础工作流定义中此操作的详细信息，请参阅[“表”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)。

### <a name="test-your-logic-app"></a>测试逻辑应用

若要确认“创建 HTML 表”操作是否创建了预期的结果，请向自己发送一条通知，其中包含“创建 HTML 表”操作的输出。

1. 在逻辑应用中，添加一个可以向自己发送“创建 HTML 表”操作结果的操作。

1. 在该操作中，单击希望显示结果的任意位置。 当动态内容列表打开时，请在“创建 HTML 表”操作下面选择“输出”。 

   此示例使用 Office 365 Outlook 的 "**发送电子邮件**" 操作，并在电子邮件的正文中包含**输出**字段：

   ![“发送电子邮件”操作中的“输出”字段](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > 在电子邮件操作中包含 HTML 表输出时，请确保在电子邮件中操作的高级选项中，将“是 HTML”属性设置为“是”。 这样，电子邮件操作便可以正确设置 HTML 表的格式。

1. 现在，请手动运行逻辑应用。 在设计器工具栏上选择“运行”。

   根据所用的电子邮件连接器，会收到下面所示的结果：

   ![包含“创建 HTML 表”操作结果的电子邮件](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>“筛选数组”操作

若要创建一个较小的数组，其中包含的项符合特定条件，则使用**筛选器数组**操作。 然后，可以在“筛选数组”操作后面的操作中使用筛选的数组。

> [!NOTE]
> 在条件中使用的任何筛选器文本区分大小写。 此外，此操作无法更改数组中项的格式或组成部分。 
> 
> 要使操作使用“筛选数组”操作的数组输出，这些操作必须接受数组作为输入，或者必须将输出数组转换为另一种兼容格式。

如果你更喜欢在 "代码视图" 编辑器中工作，则可以将示例**筛选器数组**和将此项目中的变量操作定义**初始化**为自己的逻辑应用的基础工作流定义：[数据操作代码示例-筛选数组](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。

   本示例使用 Azure 门户以及包含一个“重复”触发器和一个“初始化变量”操作的逻辑应用。 该操作设置用于创建一个变量，该变量的初始值是包含一些示例整数的数组。 稍后测试逻辑应用时，可以手动运行应用，而无需等待触发器激发。

   > [!NOTE]
   > 尽管本示例使用了简单的整数数组，但此操作对 JSON 对象数组特别有用，在其中，可以基于对象的属性和值进行筛选。

   ![启动示例逻辑应用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. 在要创建筛选数组的逻辑应用中，执行以下步骤之一： 

   * 要在上一步中添加操作，请选择 "**新建步骤**"。

     ![添加操作](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上，以便显示 **+** 加号（）。 选择加号，然后选择 "**添加操作**"。

1. 在搜索框中，输入`filter array`作为筛选器。 从 "操作" 列表中，选择 "**筛选数组**" 操作。

   ![选择“筛选数组”操作](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. 在“从”框中，提供要筛选的数组或表达式。

   对于本示例，在“从”框中单击时，会显示动态内容列表，以便可以选择先前创建的变量：

   ![选择用于创建筛选数组的数组输出](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. 对于条件，请指定要比较的数组项，选择比较运算符，然后指定比较值。

   此示例使用`item()`函数来访问数组中的每一项，同时**筛选器数组**操作搜索值大于1的数组项：
   
   ![完成的“筛选数组”操作](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

有关基础工作流定义中此操作的详细信息，请参阅[“查询”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)。

### <a name="test-your-logic-app"></a>测试逻辑应用

若要确认“筛选数组”操作是否创建了预期的结果，请向自己发送一条通知，其中包含“筛选数组”操作的输出。

1. 在逻辑应用中，添加一个可以向自己发送“筛选数组”操作结果的操作。

1. 在该操作中，单击希望显示结果的任意位置。 当 "动态内容" 列表打开时，选择 "**表达式**"。 若要获取“筛选数组”操作的数组输出，请输入包含“筛选数组”操作名称的以下表达式：

   `@actionBody('Filter_array')`

   此示例使用 Office 365 Outlook 的 "**发送电子邮件**" 操作，并在电子邮件的正文中包含来自**ActionBody （"Filter_array"）** 表达式的输出：

   ![“发送电子邮件”操作中的操作输出](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. 现在，请手动运行逻辑应用。 在设计器工具栏上选择“运行”。

   根据所用的电子邮件连接器，会收到下面所示的结果：

   ![包含“筛选数组”操作结果的电子邮件](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Join 操作

若要创建一个包含数组中所有项的字符串，并使用特定分隔符字符分隔这些项，请使用**联接**操作。 然后，可以在“联接”操作后面的操作中使用该字符串。

如果你更喜欢在 "代码视图" 编辑器中工作，则可以将此示例中的 "**联接**" 和 "**初始化变量**" 操作定义复制到自己的逻辑应用的基础工作流定义中：[数据操作代码示例-联接](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。

   本示例使用 Azure 门户以及包含一个“重复”触发器和一个“初始化变量”操作的逻辑应用。 此操作设置用于创建一个变量，该变量的初始值是包含一些示例整数的数组。 稍后测试逻辑应用时，可以手动运行应用，而无需等待触发器激发。

   ![启动示例逻辑应用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. 在要从数组创建字符串的逻辑应用中，执行以下步骤之一：

   * 要在上一步中添加操作，请选择 "**新建步骤**"。

     ![添加操作](./media/logic-apps-perform-data-operations/add-join-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上，以便显示 **+** 加号（）。 选择加号，然后选择 "**添加操作**"。

1. 在搜索框中，输入`join`作为筛选器。 在操作列表中选择此操作：**Join**

   ![选择 "联接" 操作](./media/logic-apps-perform-data-operations/select-join-action.png)

1. 在“从”框中，提供包含要联接的项（字符串形式）的数组。

   对于本示例，在“从”框中单击时，会显示动态内容列表，以便可以选择先前创建的变量：  

   ![选择用于创建字符串的数组输出](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. 在“联接方式”框中，输入用于分隔每个数组项的字符。 

   本示例使用冒号 (:) 作为分隔符。

   ![提供分隔符](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

有关基础工作流定义中此操作的详细信息，请参阅[“联接”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)。

### <a name="test-your-logic-app"></a>测试逻辑应用

若要确认“联接”操作是否创建了预期的结果，请向自己发送一条通知，其中包含“联接”操作的输出。

1. 在逻辑应用中，添加一个可以向自己发送“联接”操作结果的操作。

1. 在该操作中，单击希望显示结果的任意位置。 当动态内容列表打开时，请在“联接”操作下面选择“输出”。 

   此示例使用 Office 365 Outlook 的 "**发送电子邮件**" 操作，并在电子邮件的正文中包含**输出**字段：

   ![“发送电子邮件”操作中的“输出”字段](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. 现在，请手动运行逻辑应用。 在设计器工具栏上选择“运行”。

   根据所用的电子邮件连接器，会收到下面所示的结果：

   ![包含“联接”操作结果的电子邮件](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Parse JSON 操作

若要引用或访问 JavaScript 对象表示法（JSON）内容中的属性，可以通过使用**PARSE JSON**操作为这些属性创建用户友好的字段或令牌。 这样，在为逻辑应用指定输入时，便可以从动态内容列表中选择这些属性。 对于此操作，可以提供 JSON 架构，或者从示例 JSON 内容或有效负载生成 JSON 架构。

如果你更喜欢在 "代码视图" 编辑器中工作，则可以将此示例中的示例**分析 JSON**和**初始化变量**操作定义复制到自己的逻辑应用的基础工作流定义中：[数据操作代码示例-分析 JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。

   本示例使用 Azure 门户以及包含一个“重复”触发器和一个“初始化变量”操作的逻辑应用。 该操作设置用于创建一个变量，该变量的初始值是包含属性和值的 JSON 对象。 稍后测试逻辑应用时，可以手动运行应用，而无需等待触发器激发。

   ![启动示例逻辑应用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. 在要分析 JSON 内容的逻辑应用中，执行以下步骤之一：

   * 要在上一步中添加操作，请选择 "**新建步骤**"。

     ![添加操作](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上，以便显示 **+** 加号（）。 选择加号，然后选择 "**添加操作**"。

1. 在搜索框中，输入`parse json`作为筛选器。 从 "操作" 列表中，选择 "**分析 JSON** " 操作。

   ![选择“分析 JSON”操作](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. 在“内容”框中，提供要分析的 JSON 内容。

   对于本示例，在“内容”框中单击时，会显示动态内容列表，以便可以选择先前创建的变量：

   ![选择“分析 JSON”操作的 JSON 对象](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. 输入 JSON 架构用于描述所要分析的 JSON 内容。

   在本示例中，JSON 架构如下所示：

   ![为要分析的 JSON 对象提供 JSON 架构](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   如果没有架构，可以从要分析的 JSON 内容或有效负载生成该架构。 
   
   1. 在“分析 JSON”操作中，选择“使用示例有效负载生成架构”。

   1. 在 "**输入或粘贴示例 json 负载**" 下，提供 json 内容，然后选择 "**完成**"。

      ![输入用于生成架构的 JSON 内容](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

有关基础工作流定义中此操作的详细信息，请参阅[“分析 JSON”操作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

### <a name="test-your-logic-app"></a>测试逻辑应用

若要确认“分析 JSON”操作是否创建了预期的结果，请向自己发送一条通知，其中包含“分析 JSON”操作的输出。

1. 在逻辑应用中，添加一个可以向自己发送“分析 JSON”操作结果的操作。

1. 在该操作中，单击希望显示结果的任意位置。 当动态内容列表打开时，可在“分析 JSON”操作下面选择已分析的 JSON 内容中的属性。

   此示例使用 Office 365 Outlook 的 "**发送电子邮件**" 操作，并在电子邮件正文中包含 "**名字**"、"**姓氏**" 和 "**电子邮件**" 字段：

   ![“发送电子邮件”操作中的 JSON 属性](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   下面是完成的电子邮件操作：

   ![完成的电子邮件操作](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. 现在，请手动运行逻辑应用。 在设计器工具栏上选择“运行”。 

   根据所用的电子邮件连接器，会收到下面所示的结果：

   ![包含“联接”操作结果的电子邮件](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>选择操作

若要创建一个数组，该数组包含使用现有数组中的值生成的 JSON 对象，请使用**Select**操作。 例如，可以通过指定每个 JSON 对象必须包含的属性，以及将源数组中的值映射到这些属性的方式，来为整数数组中的每个值创建一个 JSON 对象。 尽管可以更改这些 JSON 对象中的组成部分，但输出数组的项数始终与源数组相同。

> [!NOTE]
> 要使操作使用“选择”操作的数组输出，这些操作必须接受数组作为输入，或者必须将输出数组转换为另一种兼容格式。 

如果你更喜欢在代码视图编辑器中工作，则可以将此**示例中**的变量操作定义复制到你自己的逻辑应用的基础工作流定义，并将其**初始化**为你自己的逻辑应用：[数据操作代码示例-选择](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开逻辑应用。

   本示例使用 Azure 门户以及包含一个“重复”触发器和一个“初始化变量”操作的逻辑应用。 该操作设置用于创建一个变量，该变量的初始值是包含一些示例整数的数组。 稍后测试逻辑应用时，可以手动运行应用，而无需等待触发器激发。

   ![启动示例逻辑应用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. 在要创建数组的逻辑应用中，执行以下步骤之一： 

   * 要在上一步中添加操作，请选择 "**新建步骤**"。

     ![添加操作](./media/logic-apps-perform-data-operations/add-select-action.png)

   * 若要在步骤之间添加操作，请将鼠标移到连接箭头上，以便显示 **+** 加号（）。 选择加号，然后选择 "**添加操作**"。

1. 在“选择操作”下，选择“内置”。 在搜索框中，输入`select`作为筛选器。 从 "操作" 列表中，选择 "**选择**操作"。

   ![选择“选择”操作](./media/logic-apps-perform-data-operations/select-select-action.png)

1. 在“从”框中，指定所需的源数组。

   对于本示例，在“从”框中单击时，会显示动态内容列表，以便可以选择先前创建的变量：

   ![选择“选择”操作的源数组](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. 在“映射”框的左列中，提供要将源数组中每个值分配到的属性名称。 在右列中指定一个表达式，用于表示要将该属性分配到的值。

   此示例将 "Product_ID" 指定为属性名称，以通过在访问每个数组项的表达式`item()`中使用函数来分配整数数组中的每个值。 

   ![指定要创建的数组的 JSON 对象属性和值](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   下面是完成的操作：

   ![完成的“选择”操作](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

有关基础工作流定义中此操作的详细信息，请参阅[“选择”操作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

### <a name="test-your-logic-app"></a>测试逻辑应用

若要确认“选择”操作是否创建了预期的结果，请向自己发送一条通知，其中包含“选择”操作的输出。

1. 在逻辑应用中，添加一个可以向自己发送“选择”操作结果的操作。

1. 在该操作中，单击希望显示结果的任意位置。 当 "动态内容" 列表打开时，选择 "**表达式**"。 若要获取“选择”操作的数组输出，请输入包含“选择”操作名称的以下表达式：

   `@actionBody('Select')`

   此示例使用 Office 365 Outlook 的 "**发送电子邮件**" 操作，并在电子`@actionBody('Select')`邮件正文中包含来自表达式的输出：

   ![“发送电子邮件”操作中的操作输出](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. 现在，请手动运行逻辑应用。 在设计器工具栏上选择“运行”。

   根据所用的电子邮件连接器，会收到下面所示的结果：

   ![包含“选择”操作结果的电子邮件](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>后续步骤

* 了解[逻辑应用连接器](../connectors/apis-list.md)
