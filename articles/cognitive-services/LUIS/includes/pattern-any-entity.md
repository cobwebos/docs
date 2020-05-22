---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 8aef4a5eeeec27bfb2ca3edf9ff6823b88431319
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591823"
---
通过 pattern.any 实体，可找到自由格式数据，其中实体的用语方式比较晦涩，难以从话语的其余部分来确定实体的结尾。

此人力资源应用有助于员工查找公司表单。

|话语|
|--|
|**HRF-123456** 在哪里？|
|**HRF-123234** 的作者是谁？|
|**HRF-456098** 是以法语发布的吗？|

但是，每个表单都具有一个带格式的名称（如上表所使用的名称），以及一个易记名称，如 `Request relocation from employee new to the company 2018 version 5`。

具有易记表单名称的话语如下所示：

|话语|
|--|
|“公司新员工请求调职 2018 年第 5 版”在哪里？|
|“公司新员工请求调职 2018 年第 5 版”的作者是谁？|
|“公司新员工请求调职 2018 年第 5 版”是以法语发布的吗？|

此可变长度包含可能导致 LUIS 无法正确识别实体结尾位置的单词。 在模式中使用 Pattern.any 实体，可指定表单名称的开头和结尾，以便 LUIS 正确提取表单名称。

|模板话语示例|
|--|
|{FormName} 在哪里[？]|
|{FormName} 的作者是谁[？]|
|{FormName} 是以法语发布的吗[？]|

查看模式。任何[参考](../reference-entity-pattern-any.md)信息