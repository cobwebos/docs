---
title: 了解 LUIS 应用协作 - Azure | Microsoft Docs
description: LUIS 应用需要一个所有者和多个可选协作者。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 51b3958f83cd110ace27f6ee42571c05843f5aa2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265059"
---
# <a name="collaborating"></a>协作

通过 LUIS 提供的协作组成员可共同创作应用。

## <a name="luis-account"></a>LUIS 帐户
LUIS 帐户与一个 [Microsoft Live](https://login.live.com/) 帐户相关联。 每个 LUIS 帐户都分配有一个免费的[创作密钥](luis-concept-keys.md#authoring-key)，可用于创作该帐户可访问的所有 LUIS 应用。 

一个 LUIS 帐户可能有很多 LUIS 应用。

## <a name="luis-app-owner"></a>LUIS 应用所有者
创建应用的帐户是所有者。 每个应用都有一个所有者。 应用[“设置”](luis-how-to-collaborate.md)上列出了所有者。 使用此帐户可删除应用。 此外，若终结点配额达到每月限额的 75%，则还会向此帐户发送电子邮件。 

## <a name="transfer-ownership"></a>转让所有权
无法通过 LUIS 进行所有权转让，但协作者可导出应用，然后通过导入来创建该应用。 请注意，新应用的应用 ID 不同。 需训练、发布新应用，并使用新的终结点。

## <a name="luis-app-collaborators"></a>LUIS 应用协作者
应用所有者可向应用添加协作者。 所有者需要在应用[“设置”](luis-how-to-collaborate.md)上添加协作者的电子邮件地址。 协作者具有应用的完全访问权限。 如果协作者删除应用，则应用会从协作者的帐户中删除，但仍存在于所有者帐户中。 

如果想要与协作者共享多个应用，则需在每个应用中添加协作者的电子邮件。 

## <a name="managing-multiple-authors"></a>管理多个创建者
[LUIS][LUIS]网站目前不支持事务级的创作。 可允许创建者基于基础版创建独立版本。 将在下面各节中介绍两种不同的方法。

### <a name="manage-multiple-versions-inside-the-same-app"></a>在同一应用中管理多个版本
首先，每个创建者基于基础版进行[克隆](luis-how-to-manage-versions.md#clone-a-version)。 

每个创建者对自己的应用版本进行更改。 得到满意的模型后，将新版本导出到 JSON 文件中。  

导出的应用为 JSON 格式的文件，可比较所做更改。 合并这些文件，创建一个新版本的 JSON 文件。 更改 JSON 中的 versionId 属性以表示新的合并版本。 将该版本导入原始应用。 

通过此方法可获得一个活动版本、阶段版本和已发布版本的应用。 可在交互式测试窗格中比较三个版本的结果。

### <a name="manage-multiple-versions-as-apps"></a>将各版本作为应用进行管理
[导出](luis-how-to-manage-versions.md#export-version)基础版本。 各创建者导入该版本。 导入应用的创建者即为该版本的所有者。 修改应用后，导出该版本。 

导出的应用为 JSON 格式的文件，可与导出的基础版进行比较，发现所做更改。 合并这些文件，创建一个新版本的 JSON 文件。 更改 JSON 中的 versionId 属性以表示新的合并版本。 将该版本导入原始应用。

## <a name="next-steps"></a>后续步骤

了解[版本控制](luis-concept-version.md)概念。 

请参阅[应用设置](luis-how-to-collaborate.md)，了解如何在 LUIS 应用中管理协作者。

请参阅使用创作 API [向访问列表添加电子邮件](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342)。

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website