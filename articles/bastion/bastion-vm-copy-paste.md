---
title: 复制和粘贴到虚拟机和从虚拟机：Azure 堡垒
description: 在本文中，了解如何使用 Bastion 复制和粘贴到 Azure VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989529"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>复制并粘贴到虚拟机：Azure 堡垒

本文可帮助您在使用 Azure 堡垒时向虚拟机复制和粘贴文本。 在使用 VM 之前，请确保已遵循[创建堡垒主机](bastion-create-host-portal.md)的步骤。 然后，使用[RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)连接到要使用的 VM。

对于支持高级剪贴板 API 访问的浏览器，可以在本地设备和远程会话之间复制和粘贴文本，就像在本地设备上的应用程序之间复制和粘贴文本一样。 对于其他浏览器，您可以使用堡垒剪贴板访问工具调色板。

   ![允许剪贴板](./media/bastion-vm-manage/allow.png)

仅支持文本复制/粘贴。 对于直接复制和粘贴，您的浏览器可能会提示您在初始化堡垒会话时访问剪贴板。 **允许**网页访问剪贴板。

## <a name="copy-to-a-remote-session"></a><a name="to"></a>复制到远程会话

使用[Azure 门户](https://portal.azure.com)连接到虚拟机后，请完成以下步骤：

1. 将本地设备的文本/内容复制到本地剪贴板中。
1. 在远程会话期间，通过选择两个箭头启动堡垒剪贴板访问工具调色板。 箭头位于会话的左中心。

   ![工具调色板](./media/bastion-vm-manage/left.png)

   ![剪贴板](./media/bastion-vm-manage/clipboard.png)
1. 通常，复制的文本会自动显示在堡垒复制粘贴调色板上。 如果文本不存在，则将文本粘贴到调色板上的文本区域中。
1. 文本进入文本区域后，可以将其粘贴到远程会话。

   ![粘贴](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>从远程会话复制

使用[Azure 门户](https://portal.azure.com)连接到虚拟机后，请完成以下步骤：

1. 将远程会话中的文本/内容复制到远程剪贴板（使用 Ctrl-C）。

   ![工具调色板](./media/bastion-vm-manage/remote.png)
1. 在远程会话期间，通过选择两个箭头启动堡垒剪贴板访问工具调色板。 箭头位于会话的左中心。

   ![剪贴板](./media/bastion-vm-manage/clipboard2.png)
1. 通常，复制的文本会自动显示在堡垒复制粘贴调色板上。 如果文本不存在，则将文本粘贴到调色板上的文本区域中。
1. 文本进入文本区域后，可以将其粘贴到本地设备。

   ![粘贴](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。