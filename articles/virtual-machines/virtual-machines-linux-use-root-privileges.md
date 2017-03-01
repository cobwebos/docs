---
title: "使用 Linux 虚拟机的根权限 | Microsoft Docs"
description: "了解如何在 Azure 中使用 Linux 虚拟机的根权限。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: a2c106a2-dceb-43a3-9dd1-50ed77685952
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1c8c5f4e622863a0961bd5e1c7a915e9511c871f


---
# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>在 Azure 中的 Linux 虚拟机上使用根权限
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

默认情况下，禁止在 Azure 中的 Linux 虚拟机上使用 `root` 用户。 用户可以通过 `sudo` 命令使用提升的权限运行各种命令。 但是，体验可能会因系统设置方式而有所不同。

1. **SSH 密钥和密码或仅密码** - 使用证书（`.CER` 文件）或 SSH 密钥和密码，或只是用户名和密码预配虚拟机。 在此示例中，`sudo` 将会在执行命令前提示用户输入密码。
2. **仅 SSH 密钥** - 使用证书（`.cer`、`.pem` 或 `.pub` 文件）或 SSH 密钥预配虚拟机，而不使用密码。  在此示例中，`sudo` **将不会**在执行命令前提示用户输入密码。

## <a name="ssh-key-and-password-or-password-only"></a>SSH 密钥和密码，或仅密码
使用 SSH 密钥或密码身份验证登录到 Linux 虚拟机，然后使用 `sudo` 运行命令，例如：

    # sudo <command>
    [sudo] password for azureuser:

在此示例中，将会提示用户输入密码。 输入密码后，`sudo` 将使用 `root` 权限运行命令。

还可以通过编辑 `/etc/sudoers.d/waagent` 文件启用不需要密码的 sudo，例如：

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

对于用户“azureuser”，此更改可启用不需要密码的 sudo。

## <a name="ssh-key-only"></a>仅 SSH 密钥
使用 SSH 密钥身份验证登录到 Linux 虚拟机，然后使用 `sudo` 运行命令，例如：

    # sudo <command>

在此示例中，将**不会**提示用户输入密码。 按 `<enter>` 后，`sudo` 将使用 `root` 权限运行命令。




<!--HONumber=Nov16_HO3-->


