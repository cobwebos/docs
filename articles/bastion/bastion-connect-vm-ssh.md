---
title: 连接到 Linux VM 使用 Azure 堡垒 |Microsoft Docs
description: 在本文中，了解如何使用 Azure 堡垒连接到 Linux 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 572043598c71a400e154c5c2e9e6c2f1e9b4ab49
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191793"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>为 Linux 虚拟机使用 Azure 堡垒 （预览版） 使用 ssh 建立连接

本文介绍如何对安全、 无缝地通过 ssh 连接到 Azure 虚拟网络中的 Linux Vm。 可通过 Azure 门户直接连接到 VM。 在使用 Azure Bastion 时，VM 无需客户端、代理和其他软件。 有关 Azure 堡垒详细信息，请参阅[概述](bastion-overview.md)。

Azure 堡垒可用于连接到使用 SSH 为 Linux 虚拟机。 可以使用用户名/密码和 SSH 密钥进行身份验证。 您可以连接到你的 VM 使用 SSH 密钥使用：

* 手动输入的专用密钥
* 包含私钥信息的文件

SSH 私钥必须是开头的格式`"-----BEGIN RSA PRIVATE KEY-----"`结尾`"-----END RSA PRIVATE KEY-----"`。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

请确保已设置 VM 所驻留的虚拟网络的 Azure 堡垒主机。 有关详细信息，请参阅[创建 Azure 堡垒主机](bastion-create-host-portal.md)。 一旦堡垒服务预配和部署在虚拟网络，可用于连接到此虚拟网络中的任何 VM。 在此预览版中，当您使用堡垒连接，假定你使用 RDP 连接到 Windows VM 和 SSH 连接到 Linux Vm。

若要进行连接时，以下角色是必需的：

* 在虚拟机上的读取者角色
* 使用虚拟机的专用 IP 的 NIC 上的读取者角色
* Azure 堡垒资源上的读取者角色

## <a name="username"></a>连接：使用用户名和密码


1. 在中[Azure 门户](https://aka.ms/BastionHost)堡垒预览版中，导航到你想要连接到虚拟机，然后单击**Connect**。 使用 SSH 连接时，应将 VM Linux 虚拟机。
1. 单击连接后，侧栏会显示具有三个选项卡-RDP、 SSH，并堡垒。 如果堡垒已预配为虚拟网络，则堡垒选项卡处于活动状态默认情况下。 如果你未为虚拟网络进行预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。 如果没有看到**堡垒**列出，则表示尚未打开在预览门户。 打开门户使用[此链接](https://aka.ms/BastionHost)。

      ![VM 连接](./media/bastion-connect-vm-ssh/bastion.png)

1. 输入你的虚拟机到 SSH 用户名和密码。
1. 单击**Connect**按钮之后输入密钥。

## <a name="privatekey"></a>连接：手动输入私钥

1. 在中[Azure 门户](https://aka.ms/BastionHost)堡垒预览版中，导航到你想要连接到虚拟机，然后单击**Connect**。 使用 SSH 连接时，应将 VM Linux 虚拟机。
1. 单击连接后，侧栏会显示具有三个选项卡-RDP、 SSH，并堡垒。 如果堡垒已预配为虚拟网络，则堡垒选项卡处于活动状态默认情况下。 如果你未为虚拟网络进行预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。 如果没有看到**堡垒**列出，则表示尚未打开在预览门户。 打开门户使用[此链接](https://aka.ms/BastionHost)。

      ![VM 连接](./media/bastion-connect-vm-ssh/bastion.png)

1. 输入用户名，然后选择**SSH 私钥**。
1. 文本区域中输入你的私匙**SSH 私钥**（或将其直接粘贴）。
1. 单击**Connect**按钮之后输入密钥。

## <a name="ssh"></a>连接：使用私钥文件

1. 在中[Azure 门户](https://aka.ms/BastionHost)堡垒预览版中，导航到你想要连接到虚拟机，然后单击**Connect**。 使用 SSH 连接时，应将 VM Linux 虚拟机。

    ![VM 连接](./media/bastion-connect-vm-ssh/connect.png)

1. 单击连接后，侧栏会显示具有三个选项卡-RDP、 SSH，并堡垒。 如果堡垒已预配为虚拟网络，则堡垒选项卡处于活动状态默认情况下。 如果你未为虚拟网络进行预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。 如果没有看到**堡垒**列出，则表示尚未打开在预览门户。 打开门户使用[此链接](https://aka.ms/BastionHost)。

    ![VM 连接](./media/bastion-connect-vm-ssh/bastion.png)

1. 输入用户名，然后选择**SSH 私钥从本地文件**。
1. 单击**浏览**按钮 （在本地文件文件夹图标）。
1. 浏览的文件，然后单击**打开**。
1. 单击**Connect**以连接到 VM。 单击后连接、 通过 ssh 连接到此虚拟机将直接打开 Azure 门户中。 此连接是通过 HTML5 堡垒服务上的端口 443 通过你的虚拟机的专用 IP。

## <a name="next-steps"></a>后续步骤

读取[堡垒常见问题](bastion-faq.md)