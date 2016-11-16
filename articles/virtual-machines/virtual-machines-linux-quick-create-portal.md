---
title: "使用 Azure 门户创建 Linux VM | Microsoft Docs"
description: "使用 Azure 门户创建 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1446cd8892e14988ff428eaa03233f8e9aefb8a


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>使用门户在 Azure 上创建 Linux VM
本文说明如何使用 [Azure 门户](https://portal.azure.com/)创建 Linux 虚拟机。

要求如下：

* [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 公钥和私钥文件](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="sign-in"></a>登录
使用你的 Azure 帐户标识登录到 Azure 门户，然后单击左上角的“+ 新建”  ：

![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>选择 VM
在“应用商店”中单击“虚拟机”，然后从“特色应用”映像列表中选择“Ubuntu Server 14.04 LTS”。  确认底部显示的部署模型是 `Resource Manager`，然后单击“创建”。

![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>输入 VM 选项
在“基本信息”页上输入： 

* VM 的名称
* 管理员用户的用户名
* 设置为“SSH 公钥”的身份验证类型 
* 字符串形式的 SSH 公钥（位于 `~/.ssh/` 目录中）
* 资源组名称或选择现有组

单击“确定”继续操作，然后选择 VM 大小，如以下屏幕截图所示：

![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>选择 VM 大小
选择“DS1”大小（将在高级 SSD 上安装 Ubuntu），然后单击“选择”以配置设置。

![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>存储和网络
在“设置”中，保留对“存储和网络”值的默认设置，然后单击“确定”查看摘要。  **S** 表示 SSD）设置为高级 SSD。

![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>确认 VM 设置并启动
确认新 Ubuntu VM 的设置，然后单击“确定”。

![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>查找 VM NIC
打开门户仪表板并在“网络接口”中选择 NIC 

![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>查找公共 IP
打开 NIC 设置下面的公共 IP 地址菜单

![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>SSH 到 VM
使用 SSH 公钥通过 SSH 连接到公共 IP。  可以在 Mac 或 Linux 工作站中从终端直接进行 SSH 操作。 如果是在 Windows 工作站上操作，则需使用 PuTTY、MobaXTerm 或 Cygwin 通过 SSH 连接到 Linux。  如果还没有做好准备，可以参阅以下文档，了解如何准备 Windows 工作站，以便通过 SSH 连接到 Linux。

[如何在 Azure 上结合使用 SSH 密钥和 Windows](virtual-machines-linux-ssh-from-windows.md)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>后续步骤
现在，你已快速创建了一个用于测试或演示的 Linux VM。 若要创建针对基础结构自定义的 Linux VM，可以遵循以下任一文章操作。

* [Create a Linux VM on Azure using Templates（使用模板在 Azure 上创建 Linux VM）](virtual-machines-linux-cli-deploy-templates.md)
* [使用模板在 Azure 上创建受 SSH 保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Create a Linux VM using the Azure CLI（使用 Azure CLI 创建 Linux VM）](virtual-machines-linux-create-cli-complete.md)




<!--HONumber=Nov16_HO2-->


