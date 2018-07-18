---
title: Azure 内容审查器 API 参考 | Microsoft Docs
description: 了解内容审查器的内容审核和评审 API。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 94de9b91cc242e8b7e5479cacab8380adac551f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365455"
---
# <a name="content-moderator-api-reference"></a>内容审查器 API 参考

可以通过以下方式开始使用 Azure 内容审查器 API：（另请参阅[管理凭据](review-tool-user-guide/credentials.md)。）

- 在 Azure 门户中，[订阅内容审查器 API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)。
- 注册[内容审查器审阅工具](http://contentmoderator.cognitive.microsoft.com/)。 请参阅[快速入门](quick-start.md)。

## <a name="moderation-apis"></a>审查 API

可以使用以下内容审查器 API 设置审查后工作流。

| 说明 | 引用 |
| -------------------- |-------------|
| **图像审查 API**<br /><br />通过使用标记、置信度分数和其他提取的信息扫描图像并检测潜在的成人和不雅内容。 <br /><br />使用此信息发布、拒绝或查看审查后工作流中的内容。 <br /><br />| [图像审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "图像审查 API 参考")   |
| **文本审查 API**<br /><br />扫描文本内容。 返回不雅用语和个人身份信息 (PII)。 <br /><br />使用此信息发布、拒绝或查看审查后工作流中的内容。<br /><br /> | [文本审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "文本审查 API 参考")   |
| **视频审查 API**<br /><br />扫描视频并检测潜在的成人和不雅内容。 <br /><br />使用此信息发布、拒绝或查看审查后工作流中的内容。<br /><br /> | [视频审查 API 概述](video-moderation-api.md "视频审查 API 概述")   |
| **列表管理 API**<br /><br />创建并管理图像和文本的自定义排除或包含列表。 如果启用，则“图像 - 匹配”和“文本 - 屏幕”操作会将提交的内容与你的自定义列表进行模糊匹配。 <br /><br />为了提高效率，可以跳过基于机器学习的审查步骤。<br /><br /> | [列表管理 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "列表管理 API 参考")   |

## <a name="review-api"></a>评审 API

评审 API 包括以下组件：

| 说明 | 引用 |
| -------------------- |-------------|
| **作业**<br /><br /> 为图像和文本内容启动扫描并评审审查工作流。 审查作业通过使用图像审查 API 和文本审查 API 扫描你的内容。 审查作业使用已定义和默认的工作流来生成评审。 <br /><br />在人工审查器审查了自动分配的标记和预测数据并提交了内容审核决定后，评审 API 会将所有信息提交到 API 终结点。<br /><br /> | [作业参考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "作业参考")   |
| **评审**<br /><br />使用“评审”工具可直接为人工审查器创建图像或文本评审。<br /><br /> 在人工审查器审查了自动分配的标记和预测数据并提交了内容审核决定后，评审 API 会将所有信息提交到 API 终结点。<br /><br /> | [评审参考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "评审参考")   |
| **工作流**<br /><br />创建、更新和获取有关团队创建的自定义工作流的详细信息。 可以使用“评审”工具定义工作流。 <br /> <br />工作流通常使用内容审查器，但也可以使用“评审”工具中可用作连接器的某些其他 API。<br /><br /> | [工作流参考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "工作流参考")   |


