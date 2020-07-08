---
title: 查询语言
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生查询存储语言的基础知识。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3196004015046b4d3d2789745c80d323bacdced9
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985235"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>关于 Azure 数字孪生的查询语言

回忆一下，Azure 数字孪生中心是从**数字孪生**和**关系**构建的[**双子图形**](concepts-twins-graph.md)。 可以查询此关系图以获取有关它所包含的数字孪生和关系的信息。 这些查询使用类似于 SQL 的自定义查询语言编写，称为**Azure 数字孪生查询存储语言**。

若要从客户端应用将查询提交到服务，请使用 Azure 数字孪生**查询 API**。 这使开发人员可以编写查询并应用筛选器来查找孪生中的数字集，以及有关 Azure 数字孪生方案的其他信息。

## <a name="query-language-features"></a>查询语言功能

Azure 数字孪生针对大容量图形提供丰富的查询功能。 使用类似于 SQL 的语法，使用类似于 SQL 的语法（类似于[IoT 中心查询语言](../iot-hub/iot-hub-devguide-query-language.md)，具有许多可比较功能）对查询进行了说明。

下面是 Azure 数字孪生查询存储语言中可用的操作：
* 通过数字孪生的属性获取孪生。
* 通过数字孪生接口获取孪生。
* 按关系属性获取孪生。
* 获取对多个关系类型（ `JOIN` 查询）的孪生。 允许的数量有限制 `JOIN` （一个级别用于公共预览版）。
* 使用自定义函数 `IS_OF_MODEL(twinCollection, twinTypeName)` ，这允许基于克隆的[模型](concepts-models.md)进行筛选。 它支持继承。
* 使用上述任意组合（ `AND` 、 `OR` 、 `NOT` 运算符）。
* 使用标量函数： `IS_BOOL` 、 `IS_DEFINED` 、 `IS_NULL` 、 `IS_NUMBER` 、 `IS_OBJECT` 、 `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` `ENDS_WITH` 、、和。
* 使用查询比较运算符： `AND` / `OR` / `NOT` 、 `IN` / `NOT IN` 、 `STARTSWITH` / `ENDSWITH` 、 `=` 、 `!=` `<` `>` `<=` `>=` 、、、和。
* 使用延续：查询对象使用页面大小（最大为100）实例化。 可以通过重复调用方法，一次检索一页中的数字孪生 `nextAsTwin` 。

## <a name="next-steps"></a>后续步骤

了解如何编写查询，以及[如何在操作方法：查询双子图](how-to-query-graph.md)中查看客户端代码示例。