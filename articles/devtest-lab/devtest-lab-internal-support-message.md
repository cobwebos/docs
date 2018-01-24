---
title: "在 Azure 开发测试实验室中向实验室添加内部支持声明 | Microsoft Docs"
description: "了解如何在 Azure 开发测试实验室中向实验室发布内部支持声明"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: v-craic
ms.openlocfilehash: 33c2f1e9d1ffa83003b7f0dbba46a7a4588dd57a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中向实验室添加内部支持声明

使用 Azure 开发测试实验室，可通过向用户提供实验室相关支持信息的内部支持声明自定义实验室。 例如，可提供联系信息，使用户知道如何在需要有关疑难解答或访问实验室资源的帮助时获取内部支持。 还可以提供指向内部网站或常见问题解答的链接，供团队在联系支持部门之前访问。

内部支持声明用于发布通常不会频繁更改的实验室信息。 要向用户通知实验室临时信息，如实验室策略最近的更新，请参阅[在实验室中发布公告](devtest-lab-announcements.md)。

可轻松禁用或编辑不再适用的支持声明。

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>向现有实验室添加支持声明的步骤

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 如果需要，请选择“所有服务”，并从列表中选择“开发测试实验室”。 （实验室可能已显示在“所有资源”下的“仪表板”上）。
1. 从实验室列表中选择要向其添加支持声明的实验室。  
1. 在实验室的“概览”区域中，选择“配置和策略”。  

    ![“配置和策略”按钮](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. 在左侧的“设置”下面，选择“内部支持”。

    ![“内部支持”按钮](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. 要为此实验室中的用户创建内部支持消息，请将“已启用”设置为“是”。

1. 在“支持消息”字段中，输入要向实验室用户呈现的内部支持声明。 支持消息接受 Markdown。 输入消息文本时，可以在屏幕底部的“预览”区域中查看向用户显示的消息。

    ![用于创建消息的内部支持屏幕。](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. 准备好发布支持声明后，请选择“保存”。

如果不再想要向实验室用户显示此支持消息，请返回“内部支持”页，并将“已启用”设置为“否”。

## <a name="steps-for-users-to-view-the-support-message"></a>用户查看支持消息的步骤

1. 在 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)中，选择一个实验室。

1. 在实验室的“概述”区域，选择“内部支持”。  

    ![“内部支持”按钮](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. 如果发布了支持消息，用户可以从内部支持窗格中查看该消息。

    ![显示发布的支持消息的内部支持窗格](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
* 内部支持声明通常用于提供不会频繁更改的支持信息。 还可以了解如何[向实验室发布公告](devtest-lab-announcements.md)，向用户通知实验室的临时更改或更新。
* [设置策略和计划](devtest-lab-set-lab-policy.md)提供了有关如何使用自定义策略对整个订阅应用其他限制和约定的信息。