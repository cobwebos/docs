---
title: Azure 安全中心中的补救建议 |Microsoft Docs
description: 本文介绍如何在 Azure 安全中心修正建议，以保护资源并遵守安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 4bad3227e08c0fbe0d280967e45bbef9d477e1b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569129"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>在 Azure 安全中心修正建议

建议提供了有关如何更好地保护资源的意见。 可以按照建议中提供的修正步骤来实施建议。

## <a name="remediation-steps"></a>修正步骤<a name="remediation-steps"></a>

在查看完所有建议后，决定先修正哪一建议。 建议使用[安全分数影响](security-center-recommendations.md#monitor-recommendations)来帮助确定首先要执行的操作。

1. 从列表中单击建议。

1. 按照“修正步骤”部分中的说明进行操作。 每个建议都有其自己的一组指令。 以下屏幕截图显示了一些修正步骤，这些步骤用于将应用程序配置为仅允许通过 HTTPS 传输的流量。

    ![建议详细信息](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完成后，将显示一条通知，告知你修正是否成功。

## <a name="quick-fix-remediation"></a>“快速修复”修正<a name="one-click"></a>

快速修复使你能够针对多个资源快速修正某个建议。 它仅可用于特定的建议。 快速修复可简化修正，使你能够快速增加你的安全分数，提高环境的安全性。

若要实现“快速修复”修正，请执行以下操作：

1. 在带有“快速修复!”标签的建议的列表中， 单击建议。

    [![选择“快速修复!”](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. 从“不正常的资源”选项卡上，选择要对其实施建议的资源，然后单击“修正”。

    > [!NOTE]
    > 列出的某些资源可能已禁用，因为你没有相应的权限，无法修改它们。

1. 在确认框中，阅读修正详细信息和影响。

    ![快速修复](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 影响在单击“修正”后打开的“修正资源”窗口的灰色框中列出。 其中列出了在继续进行“快速修复”修正时会发生哪些更改。

1. 请插入相关参数（如有必要），并批准修正。

    > [!NOTE]
    > 修正完成后可能需要几分钟时间，才能在“正常的资源”选项卡中看到资源。若要查看修正操作，请查阅[活动日志](#activity-log)。

1. 完成后，将显示一条通知，告知你修正是否成功。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>活动日志中的“快速修复”修正日志记录 <a name="activity-log"></a>

修正操作使用模板部署或 REST PATCH API 调用，将配置应用于资源。 这些操作记录在 [Azure 活动日志](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍了如何在安全中心修正建议。 若要了解有关安全中心的详细信息，请参阅以下主题：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
