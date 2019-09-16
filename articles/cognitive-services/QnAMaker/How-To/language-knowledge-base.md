---
title: 非英语知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 支持多种语言版本的知识库内容。 但是，每个 QnA Maker 服务应保留一种语言。 面向特定的 QnA Maker 服务创建的第一个知识库会设置该服务的语言。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961470"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker 的知识库内容的语言支持

QnA Maker 支持多种语言版本的知识库内容。 但是，每个 QnA Maker 服务应保留一种语言。 面向特定的 QnA Maker 服务创建的第一个知识库会设置该服务的语言。 有关支持的语言的完整列表，请参阅[此处](../Overview/languages-supported.md)。

会从正在提取的数据源的内容中自动识别语言。 创建新的 QnA Maker 服务和该服务中的新知识库后，可以验证是否已正确设置该语言。

1. 导航到 [Azure 门户](https://portal.azure.com/)。

1. 选择“资源组”并导航到已部署 QnA Maker 服务的资源组，然后选择“Azure 搜索”资源。

    ![选择“Azure 搜索”资源](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. 选择 "**索引**"，然后选择 " **testkb** " 索引。 这是创建的第一个 Azure 搜索索引，其中包含该服务中所有知识库的保存内容。 

1. 选择**字段**可以查看索引中的字段。

1. `questions` 和`answer`字段的分析器列设置为特定语言。 在从导入的文件和 Url 创建知识库的过程中，会自动检测到此语言。 一旦创建资源，就不能更改此语言。

    ![选定分析器](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure 机器人服务创建 QnA 机器人](../Tutorials/create-qna-bot.md)
