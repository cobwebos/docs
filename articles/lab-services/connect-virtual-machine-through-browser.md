---
title: 通过浏览器连接到虚拟机 - Azure |微软文档
description: 了解如何通过浏览器连接到虚拟机。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974271"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>通过浏览器连接到虚拟机 

DevTest 实验室与[Azure 堡垒](https://docs.microsoft.com/azure/bastion/)集成 ，使您能够通过浏览器连接到虚拟机。 有关如何在 DevTest 实验室中启用此功能的信息，请参阅[在实验室虚拟机上启用浏览器连接](enable-browser-connection-lab-virtual-machines.md)。

启用*浏览器连接*后，实验室用户可以通过浏览器访问虚拟机。  

## <a name="create-a-lab-virtual-machine"></a>创建实验室虚拟机

您首先需要在虚拟网络中创建实验室虚拟机，该虚拟网络在其上配置了堡垒。 选择您创建的第二**个子网**，而不是 AzureBastion 子网。 您可以通过访问 **"高级设置"** 选项卡在虚拟机创建期间选择虚拟网络。

![创建虚拟机](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>在浏览器中启动虚拟机

创建虚拟机后，您可以通过单击*浏览器连接*按钮并输入计算机的用户名和密码在浏览器中启动它。  

![在浏览器中启动](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>后续步骤

[向 Azure 开发测试实验室中的实验室添加 VM](devtest-lab-add-vm.md)
