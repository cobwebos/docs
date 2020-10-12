---
title: Azure 堡垒：查看虚拟机会话：全屏
description: 了解如何在 Azure Bastion 中将虚拟机视图更改为全屏并回到浏览器中进行 RDP 或 SSH 连接。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: fa795d7b066733aba54f8612bbabecf264a11c70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270285"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>将 VM 会话更改为全屏视图：Azure Bastion

本文可帮助你在浏览器中将虚拟机视图更改为全屏，然后再改回来。 使用 VM 之前，请确保已按照[创建 Bastion 主机](bastion-create-host-portal.md)的步骤进行操作。 然后，使用 [RDP](bastion-connect-vm-rdp.md) 或 [SSH](bastion-connect-vm-ssh.md) 连接到要处理的 VM。

## <a name="launch-the-clipboard-tool"></a>启动剪贴板工具

在远程会话期间，通过选择位于会话左中位置的两个箭头来启动 Bastion 剪贴板访问工具面板。

![工具](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>选择全屏

选择“全屏”按钮，将会话切换到全屏体验。 切换后，会话将重新初始化为全屏。

![全屏](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
了解如何在 Azure VM 中进行[复制和粘贴](bastion-vm-copy-paste.md)。
