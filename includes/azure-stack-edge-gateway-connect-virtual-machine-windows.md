---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 8d6c3125d0109ae9005414add27f9aa08e932e49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083199"
---
通过在 VM 创建过程中传递的 IP，使用远程桌面协议 (RDP) 连接到 Windows VM。

1. 在客户端上，启动 RDP。 中转到 " **开始** " 并键入 " **mstsc**"。
1. 输入 vm 的 IP 地址以及在 VM 模板参数文件中使用的访问凭据。

    ![通过 RDP 连接到 Windows VM](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)
1. 你可能需要批准连接到不受信任的计算机。 
1. 你现在已登录到设备上运行的 VM。 