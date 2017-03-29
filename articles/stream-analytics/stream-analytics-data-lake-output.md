---
title: "流分析 Data Lake Store 输出 | Microsoft Docs"
description: "在流分析作业中配置 Azure Data Lake Store 的身份验证和授权"
keywords: 
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2b4a10c77ae02ac0e9eeecf6d7d6ade6e4c33115
ms.openlocfilehash: 5d0a86742e4b238b94855464b2528db32b80526b
ms.lasthandoff: 01/25/2017


---
# <a name="stream-analytics-data-lake-store-output"></a>流分析 Data Lake Store 输出
流分析作业支持几种输出方法，其中一种方法为 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)。 Azure Data Lake Store 是一个企业范围的超大规模存储库，适用于大数据分析工作负荷。 Data Lake Store 可让你存储任何大小、类型和引入速度的数据，以便进行运行和探索分析。

## <a name="authorize-a-data-lake-store-account"></a>授权 Data Lake Store 帐户
1. 在 Azure 管理门户中选择 Data Lake Store 作为输出时，将会提示用户通过 Azure 经典门户对现有 Data Lake Store 的使用进行授权或请求对 Data Lake Store 的访问权限。
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. 如果已有权访问 Data Lake Store，请单击“立即授权”，在很短的时间内将会弹出一个页面，指示“正在重定向到授权...”。 该页会自动关闭，并将向你显示一个页面，可在其中配置 Data Lake Store 输出。

如果尚未注册 Data Lake Store，则可以打开“立即注册”链接发起该请求，或者按照[入门说明](../data-lake-store/data-lake-store-get-started-portal.md)操作。

## <a name="configure-the-data-lake-store-output-properties"></a>配置 Data Lake Store 输出属性
Data Lake Store 帐户身份验证完成后，你可以为自己的 Data Lake Store 输出配置属性。 下表列出了用于配置 Data Lake Store 输出的属性名称及其说明。

<table>
<tbody>
<tr>
<td><B>属性名称</B></td>
<td><B>说明</B></td>
</tr>
<tr>
<td>输出别名</td>
<td>该名称是在查询中使用的友好名称，用于将查询输出定向到此 Data Lake Store。</td>
</tr>
<tr>
<td>Data Lake Store 帐户</td>
<td>存储帐户的名称（你正在向该存储帐户发送输出）。 你将看到 Data Lake Store 帐户的下拉列表，登录门户的用户可访问该下拉列表。</td>
</tr>
<tr>
<td>路径前缀模式 [<I>可选</I>]</td>
<td>用于对指定 Data Lake Store 帐户中的文件进行编写的文件路径。 <BR>{date}、{time}<BR>示例 1：folder1/logs/{date}/{time}<BR>示例 2：folder1/logs/{date}</td>
</tr>
<tr>
<td>日期格式 [<I>可选</I>]</td>
<td>如果在前缀路径中使用日期令牌，你可以选择组织文件所采用的日期格式。 示例：YYYY/MM/DD</td>
</tr>
<tr>
<td>时间格式 [<I>可选</I>]</td>
<td>如果在前缀路径中使用时间令牌，你可以选择组织文件所采用的时间格式。 目前唯一支持的值是 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>输出数据的序列化格式。 支持 JSON、CSV 和 Avro。</td>
</tr>
<tr>
<td>编码</td>
<td>如果是 CSV 或 JSON 格式，则必须指定一种编码格式。 目前只支持 UTF-8 这种编码格式。</td>
</tr>
<tr>
<td>分隔符</td>
<td>仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。</td>
</tr>
<tr>
<td>格式</td>
<td>仅适用于 JSON 序列化。 分隔行指定了通过新行分隔各个 JSON 对象，从而格式化输出。 数组指定输出将被格式化为 JSON 对象的数组。</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>续订 Data Lake Store 授权
目前，有一个限制：即需要每隔 90 天对身份验证令牌进行手动刷新，适用于使用 Data Lake Store 输出的所有作业。 如果作业创建后或上次身份验证后更改了密码，你还需要重新对你的 Data Lake Store 帐户进行身份验证。 此问题的症状是没有作业输出，并且操作日志中的错误指示需要重新进行身份验证。

要解决此问题，请停止正在运行的作业并转到 Data Lake Store 输出。 单击“续订授权”链接，在很短的时间内将会弹出一个页面，指示“正在重定向到授权...”。 该页会自动关闭，如果成功，将指示“授权已成功续订”。 然后需要单击该页底部的“保存”，并通过从上次停止的时间重新启动作业来继续，以避免数据丢失。

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)


