---
title: "Azure 快速入门 - 创建 Windows VM 门户 | Microsoft 文档"
description: "Azure 快速入门 - 创建 Windows VM 门户"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 56e9deea4e070a99e47b7de7c5686526a4528fb6
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>使用 Azure 门户创建 Windows 虚拟机

可以通过 Azure 门户创建 Azure 虚拟机。 此方法提供一个基于浏览器的用户界面，用于创建和配置 VM 和所有相关的 Azure 资源。

## <a name="log-in-to-azure"></a>登录 Azure 

登录到位于 http://portal.azure.com 的 Azure 门户。

## <a name="create-virtual-machine"></a>创建虚拟机

2. 单击 Azure 门户左上角的“新建”按钮。

3. 从“新建”边栏选项卡中选择“计算”，从“计算”边栏选项卡中选择“Windows Server 2016 Datacenter”，然后单击“创建”按钮。

4. 填写虚拟机“基本情况”表单。 对于“资源组”，创建一个新的资源组。 资源组是在其中创建并集中管理 Azure 资源的逻辑容器。 完成后，单击“确定”。

    ![在门户边栏选项卡中输入你的 VM 的基本信息](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. 为 VM 选择大小并单击“选择”。 

6. 在“设置”边栏选项卡上，在“使用托管磁盘”下选择“是”，为其余设置保留默认值，然后单击“确定”。

7. 在摘要页上，单击“确定”以开始虚拟机部署。

8. 若要监视部署状态，请单击虚拟机。 可以在 Azure 门户仪表板上或者通过从左侧菜单中选择“虚拟机”来找到该 VM。 创建 VM 后，状态将从“正在部署”更改为“正在运行”。

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

在部署完成后，创建到虚拟机的远程桌面连接。

1. 单击虚拟机边栏选项卡上的“连接”按钮。 随后将创建并下载远程桌面协议文件（.rdp 文件），该文件类似于用于连接计算机的快捷方式。 打开此文件以连接到你的 VM。

    ![门户 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. 在远程桌面连接窗口中单击“连接”。

    ![门户 10](./media/virtual-machine-quick-start/portal-quick-start-10.png) 

4. 输入在创建虚拟机时指定的用户名和密码，然后单击“确定”。

5. 你可能会收到证书警告，请单击“是”以继续连接。

## <a name="delete-virtual-machine"></a>删除虚拟机

当不再需要时，删除资源组、虚拟机以及所有相关资源。 若要执行此操作，请从虚拟机边栏选项卡中选择资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

[安装角色和配置防火墙教程](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[浏览 VM 部署 CLI 示例](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
