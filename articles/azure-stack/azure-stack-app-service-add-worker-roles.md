---
title: "横向扩展应用程序服务-Azure 堆栈中的辅助角色 |Microsoft 文档"
description: "缩放 Azure 堆栈应用程序服务的详细的指南"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: a9be9011062f07d59842d417bf6761ec81c39275
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Azure 堆栈上的 app Service： 添加更多的基础结构或辅助角色
*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*  

本文档提供有关如何在 Azure 堆栈的基础结构和辅助角色上缩放 App Service 的说明。 它不包含用于创建其他辅助角色来支持的任何大小的应用程序的步骤。

> [!NOTE]
> 如果你的 Azure 堆栈环境不具有多个 96 GB 的 RAM 你可能会遇到困难增加额外容量。

默认情况下，Azure 堆栈上的 app Service 支持免费和共享辅助角色层。 若要添加其他辅助角色层，你需要添加更多的辅助角色。

如果你不确定什么与 Azure 堆栈安装上的默认值 App Service 一起部署，你可以查看中的其他信息[App Service 上 Azure 堆栈概述](azure-stack-app-service-overview.md)。

Azure 堆栈上的 azure App Service 部署使用虚拟机规模集的所有角色，并因此可充分利用此工作负荷的缩放功能。 因此，所有辅助角色层缩放是通过应用程序服务管理员联系。

添加其他工作进程直接在应用程序服务资源提供程序管理员联系。

1. 服务管理员身份登录到 Azure 堆栈管理门户。

2. 浏览到**应用程序服务**。

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. 单击“角色”。 你看到的所有部署的应用程序服务角色的明细。

4. 右键单击你想要缩放，然后单击类型的行**ScaleSet**。

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. 单击**缩放**，选择你想要扩展到，然后单击的实例数**保存**。

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure 堆栈上的 app Service 现在将添加更多的虚拟机、 将其配置，安装所有所需的软件，和将其标记为准备此过程完成后。 此过程可能需要大约 80 分钟。

7. 你可以通过查看中的辅助角色监视新的角色的就绪状态的进度**角色**边栏选项卡。

完全部署，并准备就绪后，工作人员变为可供用户部署其工作负荷到它们。 以下举例说明的多个可用的定价层默认情况下。 如果有可用的辅助角色有关的特定辅助角色层，用于选择相应的定价层选项不可用。

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 若要向外管理扩展，前端或发布服务器角色添加你必须向外相应的 VM 规模集扩展。 我们将添加横向扩展应用程序服务管理的未来版本中通过这些角色的能力。

若要向外管理、 前端或发布服务器角色扩展，请按照相同的步骤选择相应的角色类型。 控制器不作为缩放集部署并因此，应该两个部署在所有的生产部署的安装时间。

### <a name="next-steps"></a>后续步骤

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)
