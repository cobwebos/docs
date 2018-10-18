---
title: 在 Azure 开发测试实验室的实验室中重新部署 VM | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中重新部署虚拟机（从一个 Azure 节点移动到另一个 Azure 节点）。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 273b0f1105d8b71b90a06e2627e201b97f12a754
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095389"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室的实验室中重新部署 VM
如果无法在实验室中通过远程桌面连接连接到虚拟机 (VM)，请重新部署 VM，然后重试连接。 重新部署 VM 时，开发测试实验室会将 VM 从在其上运行该 VM 的节点移动到 Azure 基础结构中的新节点。 然后启动 VM，同时保留所有配置选项和关联资源。 此功能可为你节省排查远程桌面连接问题或在实验室中通过应用程序访问基于 Windows 的 VM 所用的时间。 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>在实验室中重新部署 VM 的步骤 
若要在 Azure 开发测试实验室的实验室中重新部署 VM，请执行以下步骤： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，并从列表中选择“开发测试实验室”。
3. 从实验室列表中，选择包含要重新部署 VM 的实验室。  
4. 在左面板中，选择“我的虚拟机”。 
5. 从 VM 列表中选择 VM。
6. 在 VM 虚拟机页上，选择左侧菜单“操作”下的“重新部署”。

    ![重新部署](media/devtest-lab-redeploy-vm/redeploy.png)
7. 阅读页面上的信息，然后选择“重新部署”按钮。 9. 检查“通知”窗口中重新部署操作的状态。

    ![重新部署状态](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>后续步骤
若要了解如何在 Azure 开发测试实验室中调整 VM 大小，请参阅[调整 VM 大小](devtest-lab-resize-vm.md)。


