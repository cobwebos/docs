---
title: "使用 Azure Machine Learning Workbench 的“按示例合并列”转换"
description: "“按示例合并列”转换的参考文档"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 469fefa48ea795a56b0e7525f99634c5149a780f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="combine-columns-by-example-transformation"></a>“按示例合并列”转换
此转换允许用户通过组合多个列的值来添加一个新列。 用户可以指定分隔符或提供组合值的示例来执行此转换。 在用户提供组合示例时，将由“按示例派生列”转换中使用的同一个“按示例”引擎来处理转换。

## <a name="how-to-perform-this-transformation"></a>如何执行此转换

若要执行此转换，请执行以下步骤：
1. 选择要合并到一个列的两个或多个列。 
2. 从“转换”菜单中选择“按示例合并列”。 或者，右键单击任意所选列的标题，然后从上下文菜单中选择“按示例合并列”。 转换编辑器随即打开，新列添加到所选列最右列的旁边。 新列包含以默认分隔符分隔的合并值。 所选列可以通过列标头中的复选框进行标识。 可以使用复选框来添加和删除选择的列。
3. 可以更新新建列中的合并值。 更新后的值作为示例用于了解转换。
4. 单击“确定”接受转换。

### <a name="transform-editor-advanced-mode"></a>转换编辑器：高级模式

“高级模式”为合并列提供了更丰富的体验。 

选择“合并列依据”下的“分隔符”，使用户能够指定“分隔符”文本框中的字符串。 从“分隔符”文本框中选择，用于在数据网格中预览结果。 按“确定”提交转换。

选择“合并列依据”下的“示例”，使用户能够提供合并值的示例。 若要提升行作为示例，则双击网格中的行。 在文本框中键入针对提升行的预期输出。 离开“分隔符”文本框，以在数据网格中预览结果。 按“确定”提交转换。 

用户可以通过单击转换编辑器中的链接在“基本模式”和“高级模式”之间切换。

### <a name="transform-editor-send-feedback"></a>转换编辑器：发送反馈

单击“发送反馈”链接，打开“反馈”对话框，其中注释框已预填充所提供的示例用户。 用户应查看注释框中的内容并提供更多详细信息，帮助我们了解问题。 如果用户不希望与 Microsoft 共享数据，则应在单击“发送反馈”按钮之前删除预填充的示例数据。 

### <a name="editing-existing-transformation"></a>编辑现有的转换

用户可以通过选择转换步骤的“编辑”选项编辑现有的“按示例合并列”转换。 单击“编辑”在“基本模式”中打开转换编辑器。 用户可以通过单击标头中的链接输入“高级模式”。 在创建转换过程中提供的所有示例都显示在此处。

## <a name="example-using-separators"></a>使用分隔符的示例

在本例中使用逗号和空格作为分隔符来合并街道、城市、州和邮政编码列。

|街道|城市|State|ZIP|列|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th Way|REDMOND|WA|98052|16011 N.E. 36th Way, REDMOND, WA, 98052|
|16021 N.E. 36th Way|REDMOND|WA|98052|16021 N.E. 36th Way, REDMOND, WA, 98052|
|16031 N.E. 36th Way|REDMOND|WA|98052|16031 N.E. 36th Way, REDMOND, WA, 98052|
|16041 N.E. 36th Way|REDMOND|WA|98052|16041 N.E. 36th Way, REDMOND, WA, 98052|
|16051 N.E. 36th Way|REDMOND|WA|98052|16051 N.E. 36th Way, REDMOND, WA, 98052|
|16061 N.E. 36th Way|REDMOND|WA|98052|16061 N.E. 36th Way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|3460 157th Avenue NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th Avenue N.E.|REDMOND|WA|98052|3240 157th Avenue N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>使用按示例的示例

粗体显示的值作为示例提供。

|日期|月份|年龄|小时|分钟|秒|合并的列|
|:----|:----|:----|:----|:----|:----|:----|
|13|10 月|2016|15|01|23|**13-Oct-2016 15:01:23 PDT**|
|16|10 月|2016|16|22|33|16-Oct-2016 15:01:33 PDT|
|17|10 月|2016|12|43|12|17-Oct-2016 15:01:12 PDT|
|12|11 月|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|11 月|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|1 月|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|3 月|2017|01|55|25|2016 年 3 月 23 日 15:01:25（太平洋夏令时）|
|16|4 月|2017|11|34|36|16-Apr-2016 15:01:36 PDT|

