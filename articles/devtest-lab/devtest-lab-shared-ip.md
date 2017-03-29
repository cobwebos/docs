---
title: "了解 Azure 开发测试实验室中的共享的 IP 地址 |Microsoft 文档"
description: "了解 Azure 开发测试实验室如何使用共享 IP 地址以最大程度地减少访问你的实验室 VM 所需的公共 IP 地址。"
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 905357b9e2262b86cde31874287cc0b89eef4815
ms.lasthandoff: 03/17/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>了解 Azure 开发测试实验室中的共享的 IP 地址

Azure 开发测试实验室使用共享 IP 地址以最大程度地减少访问你的各个实验室 VM 所需的公共 IP 地址数。  本文介绍了共享 IP 的工作原理及其相关的配置选项。

## <a name="shared-ip-setting"></a>共享 IP 设置

创建实验室时，它位于虚拟网络的一个子网中。  默认情况下，创建此子网时会将“启用共享公共 IP”设置为“是”。  此配置会为整个子网配置一个公共 IP 地址。  若要深入了解如何配置虚拟网络和子网，请参阅[在 Azure 开发测试实验室中配置虚拟网络](devtest-lab-configure-vnet.md)。

![新建实验室子网](media/devtest-lab-shared-ip/lab-subnet.png)

在此实验室中创建的任何 VM 都将默认设置为使用共享 IP。  创建 VM 时，可以在“高级设置”边栏选项卡中的“IP 地址配置”下找到此设置。

![新建 VM](media/devtest-lab-shared-ip/new-vm.png)

无论何时向子网中添加启用了共享 IP 的 VM，都会在公共 IP 地址上分配一个用于转发到 VM 上的 RDP 端口的 TCP 端口。  

## <a name="using-the-shared-ip"></a>使用共享 IP

你可以在 RDP 客户端中使用 IP 地址或者完全限定的域名（后跟一个冒号和端口）连接到 VM 上的远程桌面。  例如，在下图中，用来连接到 VM 的 RDP 地址是 `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`。  另外，还可以从 Azure 门户中选择“连接”按钮来下载预配置的 RDP 文件。

![VM 示例](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>后续步骤

* [在 Azure 开发测试实验室中定义实验室策略](devtest-lab-set-lab-policy.md)
* [在 Azure 开发测试实验室中配置虚拟网络](devtest-lab-configure-vnet.md)






