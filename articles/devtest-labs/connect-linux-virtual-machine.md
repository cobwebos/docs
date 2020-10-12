---
title: 在 Azure 开发测试实验室中连接到 Linux 虚拟机
description: '了解如何在 (Azure 开发测试实验室的实验室中连接到 Linux 虚拟机) '
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86531079"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a> (Azure 开发测试实验室连接到实验室中的 Linux VM) 
本文介绍如何在实验室中连接到 Linux VM。 

## <a name="connect-to-a-linux-vm"></a>连接到 Linux VM
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，搜索并选择 " **开发测试实验室**"。 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;":::    
1. 从实验室列表中，选择 **实验室**。

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;" 列表中选择你的 Linux VM。 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;" 页上，可以看到虚拟机的完全限定域名 (FQDN) 或 IP 地址。 你还可以查看下图所示的端口。

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="搜索并选择 &quot;开发测试实验室&quot;" 按钮仍灰显。 这是由设计决定的。
6.  使用 SSH 连接到 Linux VM。 下面的示例将连接到具有 FQDN 的 VM `mydtl07172452621450000.eastus.cloudapp.azure.com` ，并将用户名 `vmuser` 和端口连接起来 `51637` 。 输入用于连接到 VM 的用户的密码。 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    你可以使用 [Putty](https://www.putty.org/) 等工具或任何其他 SSH 客户端连接到 VM。 

    使用 SSH 进行连接后，可以 ([xfce](https://www.xfce.org)) 和远程桌面 ([xrdp](http://xrdp.org)) 安装和配置桌面环境。  有关详细信息，请参阅 [安装和配置远程桌面以连接到 Azure 中的 LINUX VM](../virtual-machines/linux/use-remote-desktop.md)。 

## <a name="next-steps"></a>后续步骤
[如何：连接到 Windows VM](connect-windows-virtual-machine.md)
