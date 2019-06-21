---
title: 将虚拟机会话视图更改为 Azure 堡垒在全屏幕 |Microsoft Docs
description: 在本文中，了解如何将视图更改为全屏显示。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191546"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>将更改为 vm 会话的全屏视图：Azure 堡垒 （预览版）

本文可帮助你更改虚拟机视图为全屏显示后再重新登录你的浏览器。 在使用 VM 之前，请确保你已按相关步骤[创建的堡垒主机](bastion-create-host-portal.md)。 然后，连接到你想要使用使用 VM [RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="launch-the-clipboard-tool"></a>启动剪贴板工具

在远程会话中，选择位于左侧中心会话的两个箭头启动堡垒剪贴板访问工具调色板。

![工具](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>选择全屏显示

选择**全屏**按钮以切换到全屏体验的会话。 一旦您切换时，会话将重新初始化对全屏幕。

![全屏显示](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>后续步骤

读取[堡垒常见问题解答](bastion-faq.md)。
了解如何[复制和粘贴](bastion-vm-copy-paste.md)传入和传出 Azure VM。