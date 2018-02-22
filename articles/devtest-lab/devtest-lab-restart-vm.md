---
title: "在 Azure 开发测试实验室的实验室中重启 VM | Microsoft Docs"
description: "了解如何在 Azure 开发测试实验室中重启虚拟机"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: v-craic
ms.openlocfilehash: e784b6ebffa215fc090af0f915900a41972fb778
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室的实验室中重启 VM
遵循本文所述步骤，可以轻松快速地在开发测试实验室中重启虚拟机。 在重启 VM 之前请考虑以下内容：

- VM 必须正在运行才能启用重启功能。
- 如果用户连接到正在运行的 VM，执行重启时，他们必须在 VM 启动备份之后重新连接一次。
- 如果重启 VM 时正在应用某个项目，则会出现一条警告，提示可能未应用该项目。 

    ![在应用项目时重启所出现的警告](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > 如果 VM 在应用项目时停止，作为备选项，可使用重启 VM 功能来解决此问题。
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室的实验室中重启 VM 的步骤
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表中，选择包含要重启 VM 的实验室。  
1. 在左面板中，选择“我的虚拟机”。 
1. 从 VM 列表中选择正在运行的 VM。
1. 在 VM 管理窗格顶部，选择“重启”。  

    ![“重启 VM”按钮](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. 在窗口右上角，选择“通知”图标，监视重启状态。

    ![查看 VM 重启的状态](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

还可在“我的虚拟机”列表中选择省略号 (...)，重启运行中的 VM。

![通过省略号重启 VM](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>后续步骤
* 重启 VM 后，即可在其管理窗格中选择“连接”重新连接到 VM。
* 浏览[开发测试实验室 Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/Samples)
