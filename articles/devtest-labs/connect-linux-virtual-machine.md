---
title: 在 Azure 开发测试实验室中连接到 Linux 虚拟机
description: 了解如何在实验室中连接到 Linux 虚拟机（Azure 开发测试实验室）
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531079"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>连接到实验室中的 Linux VM （Azure 开发测试实验室）
本文介绍如何在实验室中连接到 Linux VM。 

## <a name="connect-to-a-linux-vm"></a>连接到 Linux VM
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，搜索并选择 "**开发测试实验室**"。 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="搜索并选择 开发测试实验室":::    
1. 从实验室列表中，选择**实验室**。

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="选择实验室":::            
1. 在实验室的主页上，从 "**我的虚拟机**" 列表中选择你的 Linux VM。 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="选择 Linux VM":::        
5. 在 "**概述**" 页上，可以看到 VM 的完全限定的域名（FQDN）或 IP 地址。 你还可以查看下图所示的端口。

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="VM 的完全限定的域名":::    

    请注意，即使 VM 已启动，"**连接**" 按钮仍灰显。 这是由设计决定的。
6.  使用 SSH 连接到 Linux VM。 下面的示例将连接到具有 FQDN 的 VM `mydtl07172452621450000.eastus.cloudapp.azure.com` ，并将用户名 `vmuser` 和端口连接起来 `51637` 。 输入用于连接到 VM 的用户的密码。 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    你可以使用[Putty](https://www.putty.org/)等工具或任何其他 SSH 客户端连接到 VM。 

    使用 SSH 进行连接后，可以安装和配置桌面环境（[xfce](https://www.xfce.org)）和远程桌面（[xrdp](http://xrdp.org)）。  有关详细信息，请参阅[安装和配置远程桌面以连接到 Azure 中的 LINUX VM](../virtual-machines/linux/use-remote-desktop.md)。 

## <a name="next-steps"></a>后续步骤
[如何：连接到 Windows VM](connect-windows-virtual-machine.md)
