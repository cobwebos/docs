---
title: "Azure 快速入门 - 创建 VM 门户 | Microsoft 文档"
description: "Azure 快速入门 - 创建 VM 门户"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>使用 Azure 门户创建 Linux 虚拟机

可以通过 Azure 门户创建 Azure 虚拟机。 此方法提供一个基于浏览器的用户界面，用于创建和配置 VM 和所有相关的 Azure 资源。

在开始之前，需要同时准备好专用和公共 SSH 密钥。 有关创建适用于 Azure 的 SSH 密钥的详细信息，请参阅[创建适用于 Azure 的 SSH 密钥](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="log-in-to-azure"></a>登录 Azure 

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-virtual-machine"></a>创建虚拟机

2. 单击 Azure 门户左上角的“新建”按钮。

3. 从“新建”边栏选项卡中选择“计算”，从“计算”边栏选项卡中选择“Ubuntu Server 16.04 LTS”，然后单击“创建”按钮。

4. 填写虚拟机“基本信息”表单。 对于“身份验证类型”，建议选择“SSH”。 粘贴“SSH 公钥”时，请务必删除所有前导或尾随空格。 对于“资源组”，请创建一个新的资源组。 资源组是在其中创建并集中管理 Azure 资源的逻辑容器。 完成后，单击“确定”。

    ![在门户边栏选项卡中输入 VM 的基本信息](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. 选择 VM 的大小，然后单击“选择”。 

    ![在门户边栏选项卡中选择 VM 的大小](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. 在“设置”边栏选项卡中的“使用托管磁盘”下选择“是”，为其余设置保留默认值，然后单击“确定”。

7. 在摘要页上，单击“确定”开始部署虚拟机。

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

完成部署后，请与虚拟机建立 SSH 连接。

1. 单击该虚拟机。 可以在 Azure 门户的主屏幕上或者通过从左侧菜单中选择“虚拟机”来找到该 VM。

2. 单击“连接”按钮。 单击“连接”按钮后，将显示可用于连接到虚拟机的 SSH 连接字符串。

    ![门户 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. 运行以下命令创建 SSH 会话。 将连接字符串替换为从 Azure 门户复制的值。

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>删除虚拟机

不再需要资源组、虚拟机和所有相关的资源时，可将其删除。 为此，请从虚拟机边栏选项卡中选择该资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

[创建高可用性虚拟机教程](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[浏览 VM 部署 CLI 示例](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

