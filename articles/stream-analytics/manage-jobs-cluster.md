---
title: 在 Azure 流分析群集中创建和删除作业
description: 了解如何在 Azure 流分析群集中管理流分析作业
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 6b475e34c01ca26abd3d8ab1d904521de19f941b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943357"
---
# <a name="create-and-delete-jobs-in-an-azure-stream-analytics-cluster"></a>在 Azure 流分析群集中创建和删除作业

可以在流分析群集上运行多个 Azure 流分析作业。 在群集上运行作业是一个简单的过程，分为两步：将作业添加到群集并启动作业。 本文介绍如何在现有群集中添加和删除作业。 如果还没有流分析群集，请按照快速入门[创建流分析群集](create-cluster.md)。

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>将流分析作业添加到群集

只有现有流分析作业才能添加到群集。 按照快速入门操作，[了解如何使用 Azure 门户创建作业](stream-analytics-quick-create-portal.md)。 如果需要将某个作业添加到群集，请执行以下步骤，将该作业添加到群集。

1. 在 Azure 门户中，找到并选择流分析群集。

1. 在“设置”下选择“流分析作业” 。 然后选择“添加现有作业”。

1. 选择要添加到群集的订阅和流分析作业。 只有与群集位于同一区域的流分析作业才能添加到群集中。

   ![将作业添加到群集](./media/manage-jobs-cluster/add-job.png)

1. 将作业添加到群集后，请导航到作业资源并[启动该作业](start-job.md#azure-portal)。 然后，该作业将开始在群集上运行。

你可以在流分析作业资源页中执行所有其他操作，例如监视、警报和诊断日志。

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>从群集中删除流分析作业

流分析作业必须先处于“已停止”状态，然后才能将其从群集中删除。 如果作业仍在运行，请先停止该作业，然后再继续执行以下步骤。

1. 找到并选择流分析群集。

1. 在“设置”下选择“流分析作业” 。

1. 选择要从该群集中删除的作业，然后选择“删除”。

   ![从群集中删除作业](./media/manage-jobs-cluster/remove-job.png)

   从流分析群集中删除作业时，该作业将返回到标准多租户环境。

## <a name="next-steps"></a>后续步骤

现在你已了解如何在 Azure 流分析群集中添加和删除作业。 接下来可以了解如何管理专用终结点和缩放群集：

* [缩放 Azure 流分析群集](scale-cluster.md)
* [在 Azure 流分析群集中管理专用终结点](private-endpoints.md)