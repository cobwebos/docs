---
title: 版本控制
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 中的版本与传统编程中的版本类似。 每个版本都是应用的即时快照。 在对应用进行更改之前，请创建新版本。 可更轻松地返回到确切应用，然后尝试将应用的意向和话语还原到以前的状态。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: diberry
ms.openlocfilehash: e71fc5bb0bd8ce54b471829df20336a9dea0ccda
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217078"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>了解如何以及何时使用 LUIS 版本

LUIS 中的版本与传统编程中的版本类似。 每个版本都是应用的即时快照。 在对应用进行更改之前，请创建新版本。 可更轻松地返回到具体版本，然后尝试将意向和言语移动到以前的状态。

通过[版本](luis-how-to-manage-versions.md)创建同一应用的不同模型。 

## <a name="version-id"></a>版本 ID
版本 ID 由字符、数字或“.”组成，且长度不得超过 10 个字符。

## <a name="initial-version"></a>初始版本
初始版本 (0.1) 是默认活动版本。 

## <a name="active-version"></a>活动版本
将[版本设置](luis-how-to-manage-versions.md#set-active-version)为活动版本意味着它当前在 [LUIS](luis-reference-regions.md) 网站上处于编辑和测试状态。 将某个版本设置为活动版本可访问其数据、进行更新以及测试和发布。

当前活动版本的名称显示在顶部左侧面板中应用名称后方。 

[![更改活动版本](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>版本和发布槽
可以发布到暂存或生产槽。 每个槽可以具有不同的版本或相同的版本。 这对于通过终结点验证模型版本之间的变化非常有用，该终结点可用于机器人或其他 LUIS 调用应用程序。 

## <a name="clone-a-version"></a>克隆版本
克隆版本以创建现有版本的副本，并将其保存为新版本。 克隆版本以使用现有版本的相同内容作为新版本的起始点。 克隆版本后，新版本将成为活动版本。 

## <a name="import-and-export-a-version"></a>导入和导出版本
可以在应用级别导入版本。 该版本将成为活动版本，并使用应用文件的“versionId”属性中的版本 ID。 还可以在版本级别导入到现有应用。 新版本将成为活动版本。 

可以在应用级别导出版本，也可以在版本级别导出版本。 唯一的区别是应用级别导出的版本是当前活动版本，而在版本级别，可以在“[设置](luis-how-to-manage-versions.md)”页上选择任意要导出的版本。 

导出的文件不包含机器学习信息，因为应用在导入后会经过重新训练。 导出的文件不包含协作者，因此在版本导入到新应用后，需要重新添加协作者。

## <a name="export-each-version-as-app-backup"></a>导出各个版本作为应用备份
为了备份 LUIS 应用，请在“[设置](luis-how-to-manage-versions.md)”页上导出每个版本。

## <a name="delete-a-version"></a>删除版本
可以从“设置”页上的“版本”列表中删除活动版本之外的所有版本。 

## <a name="version-availability-at-the-endpoint"></a>终结点上的版本可用性
训练的版本在应用[终结点](luis-glossary.md#endpoint)中不会自动可用。 为使版本在终结点中可用，必须[发布](luis-how-to-publish-app.md)或重新发布版本。 可以发布到“暂存”和“生产”，从而提供最多两个在终结点中可用的应用版本。 如果需要更多版本的应用在终结点上可用，应导出版本并重新导入到新的应用。 新的应用具有不同的应用 ID。

## <a name="collaborators"></a>协作者
所有者和所有[协作者](luis-how-to-collaborate.md)具有所有应用版本的完全访问权限。

## <a name="next-steps"></a>后续步骤

请参阅如何在应用设置页上添加[版本控制](luis-how-to-manage-versions.md)。 

了解如何为模型设计[意向](luis-concept-intent.md)。
