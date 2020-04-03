---
title: 使用 Azure 堡垒连接到 Windows 虚拟机规模集 |微软文档
description: 在本文中，了解如何使用 Azure 堡垒连接到 Azure 虚拟机缩放集。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619343"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>使用 Azure 堡垒连接到虚拟机规模集

本文演示如何使用 Azure 堡垒在 Azure 虚拟网络中安全无缝地将 RDP 到 Windows 虚拟机缩放集实例。 可以直接从 Azure 门户连接到虚拟机缩放集实例。 使用 Azure 堡垒时，VM 不需要客户端、代理或其他软件。 有关 Azure 堡垒的详细信息，请参阅[概述](bastion-overview.md)。

## <a name="before-you-begin"></a>在开始之前

请确保已为虚拟机缩放集所在的虚拟网络设置了 Azure 堡垒主机。 有关详细信息，请参阅创建[Azure 堡垒主机](bastion-create-host-portal.md)。 在虚拟网络中预配和部署 Bastion 服务后，可以使用它连接到此虚拟网络中的虚拟机规模集实例。 Bastion 假定您正在使用 RDP 连接到 Windows 虚拟机规模集，SSH 连接到 Linux 虚拟机规模集。 有关连接到 Linux VM 的信息，请参阅[连接到 VM - Linux](bastion-connect-vm-ssh.md)。

## <a name="connect-using-rdp"></a><a name="rdp"></a>使用 RDP 连接

1. 打开[Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机缩放集。

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. 导航到要连接到的虚拟机缩放集实例，然后选择 **"连接**"。 使用 RDP 连接时，虚拟机缩放集应为 Windows 虚拟机缩放集。

   ![虚拟机规模集](./media/bastion-connect-vm-scale-set/2.png)
3. 选择 **"连接"** 后，将显示一个侧栏，该边栏具有三个选项卡 - RDP、SSH 和堡垒。 从侧栏中选择 **"堡垒**"选项卡。 如果未为虚拟网络预配堡垒，则可以选择用于配置堡垒的链接。 有关配置说明，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![堡垒选项卡](./media/bastion-connect-vm-scale-set/3.png)
4. 在"堡垒"选项卡上，输入虚拟机规模集的用户名和密码，然后选择"**连接**"。

   ![连接](./media/bastion-connect-vm-scale-set/4.png)
5. 通过 Bastion 连接到此虚拟机的 RDP 连接将直接在 Azure 门户（通过 HTML5）中使用端口 443 和堡垒服务打开。

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。