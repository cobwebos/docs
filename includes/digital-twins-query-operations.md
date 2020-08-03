---
author: baanders
description: Azure 数字孪生查询操作的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486641"
---
## <a name="query-language-features"></a>查询语言功能

Azure 数字孪生针对大容量图形提供丰富的查询功能。 使用类似于 SQL 的语法，使用类似于 SQL 的语法（类似于[IoT 中心查询语言](../articles/iot-hub/iot-hub-devguide-query-language.md)，具有许多可比较功能）对查询进行了说明。

> [!NOTE]
> 所有 Azure 数字孪生查询操作都区分大小写。

下面是 Azure 数字孪生查询存储语言中可用的操作。

获取数字孪生，按 .。。
* 模型（使用 `IS_OF_MODEL` 运算符）
* 属性（包括[标记属性](../articles/digital-twins/how-to-use-tags.md)）
* 接口
* 关系
  - 关系的属性

可以通过以下操作进一步增强查询：
* 获取对多个关系类型（ `JOIN` 查询）的孪生。 
  - 允许的数量有限制 `JOIN` （一个级别用于公共预览版）。
* 仅选择最上面的查询结果（ `Select TOP` 运算符）
* 使用标量函数： `IS_BOOL` 、 `IS_DEFINED` 、 `IS_NULL` 、 `IS_NUMBER` 、 `IS_OBJECT` 、 `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` `ENDSWITH` 、、和。
* 使用查询比较运算符： `IN` / `NIN` 、 `=` 、 `!=` 、 `<` 、 `>` 、 `<=` 、 `>=` 。
* 使用 `AND` `OR` `NOT` `IS_OF_MODEL` 、标量函数和比较运算符的任意组合（、、运算符）。
* 使用延续：查询对象使用页面大小（最大为100）实例化。 可以通过在后续对 API 的调用中提供继续标记，一次检索一页中的数字孪生。