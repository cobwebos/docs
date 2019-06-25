---
title: 复制并粘贴到和从虚拟机：Azure 堡垒 |Microsoft Docs
description: 在本文中，了解如何复制并粘贴到和从 Azure VM 使用堡垒。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191806"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>复制并粘贴到虚拟机：Azure 堡垒 （预览版）

本文可帮助您文本复制并粘贴到和从虚拟机使用 Azure 堡垒时。 在使用 VM 之前，请确保你已按相关步骤[创建的堡垒主机](bastion-create-host-portal.md)。 然后，连接到你想要使用使用 VM [RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

对于支持高级的剪贴板 API 访问权限的浏览器，可以复制并粘贴文本在本地设备和远程会话之间在复制和粘贴您的本地设备上的应用程序之间的相同方式。 对于其他浏览器，可以使用堡垒剪贴板访问工具调色板。

  ![允许剪贴板](./media/bastion-vm-manage/allow.png)

支持仅文本复制/粘贴。 有关直接复制和粘贴，你的浏览器可能会提示你的剪贴板访问权限堡垒会话在初始化时。 **允许**web 页后，可以访问剪贴板。

## <a name="to"></a>将复制到远程会话

连接到虚拟机使用后[Azure 门户](https://aka.ms/BastionHost)堡垒预览版：

1. 将文本/内容从本地设备复制到本地剪贴板。
1. 在远程会话中，选择两个箭头启动堡垒剪贴板访问工具调色板。 箭头都位于该会话靠左居中。

    ![工具调色板](./media/bastion-vm-manage/left.png)

    ![剪贴板](./media/bastion-vm-manage/clipboard.png)

1. 通常情况下，复制的文本自动堡垒复制粘贴选项板上显示。 如果不存在您的文本，然后将文本粘贴调色板上的文本区域中。
1. 文本区域文本后，可以将其粘贴到远程会话中。

    ![粘贴](./media/bastion-vm-manage/local.png)

## <a name="from"></a>将从远程会话复制

连接到虚拟机使用后[Azure 门户](https://aka.ms/BastionHost)堡垒预览版：

1. 远程会话中的文本/内容复制到远程剪贴板 （使用 Ctrl + C）。

    ![工具调色板](./media/bastion-vm-manage/remote.png)

1. 在远程会话中，选择两个箭头启动堡垒剪贴板访问工具调色板。 箭头都位于该会话靠左居中。

    ![剪贴板](./media/bastion-vm-manage/clipboard2.png)

1. 通常情况下，复制的文本自动堡垒复制粘贴选项板上显示。 如果不存在您的文本，然后将文本粘贴调色板上的文本区域中。
1. 文本区域文本后，可以将其粘贴到本地设备。

    ![粘贴](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>后续步骤

读取[堡垒常见问题解答](bastion-faq.md)。