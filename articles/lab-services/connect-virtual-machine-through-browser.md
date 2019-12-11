---
title: 通过浏览器连接到你的虚拟机-Azure |Microsoft Docs
description: 了解如何通过浏览器连接到你的虚拟机。
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974271"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>通过浏览器连接到你的虚拟机 

开发测试 Labs 与[Azure 堡垒](https://docs.microsoft.com/azure/bastion/)集成，使你能够通过浏览器连接到你的虚拟机。 有关如何在开发测试实验室中启用此功能的信息，请参阅在[实验室虚拟机上启用浏览器连接](enable-browser-connection-lab-virtual-machines.md)。

启用*浏览器连接*后，实验室用户可以通过浏览器访问虚拟机。  

## <a name="create-a-lab-virtual-machine"></a>创建实验室虚拟机

首先需要在虚拟网络中创建在其上配置了堡垒的实验室虚拟机。 选择创建的第二**个子网**，而不是 AzureBastionSubnet。 可以通过转到 "**高级设置**" 选项卡，在虚拟机创建过程中选择虚拟网络。

![创建虚拟机](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>在浏览器中启动虚拟机

创建虚拟机后，可以在浏览器中启动该虚拟机，方法是单击*浏览器*的 "连接" 按钮，然后输入计算机的用户名和密码。  

![在浏览器中启动](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>后续步骤

[向 Azure 开发测试实验室中的实验室添加 VM](devtest-lab-add-vm.md)
