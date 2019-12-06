---
title: include 文件
description: include 文件
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d31e30991056cc891e63347a2c88e7fc4caeab28
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875455"
---
### <a name="regions"></a>面向哪些区域提供？

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>我是否需要在虚拟机上有一个公共 IP？

不需要在使用 Azure Bastion 服务连接到的 Azure 虚拟机上有一个公共 IP。 Bastion 服务会通过虚拟网络中的虚拟机的专用 IP 打开到虚拟机的 RDP/SSH 会话/连接。

### <a name="rdpssh"></a>是否需要 RDP 或 SSH 客户端？

无需 RDP 或 SSH 客户端即可在 Azure 门户中访问 RDP/SSH 来连接到 Azure 虚拟机。 使用 [Azure 门户](https://portal.azure.com) 能够直接在浏览器中通过 RDP/SSH 来访问虚拟机。

### <a name="agent"></a>是否需要在 Azure 虚拟机中运行代理？

无需在浏览器或 Azure 虚拟机上安装代理或任何软件。 Bastion 服务没有代理，不需要任何其他软件即可使用 RDP/SSH。

### <a name="browsers"></a>支持哪些浏览器？

使用 Windows 上的 Microsoft Edge 浏览器或 Google Chrome。 对于 Apple Mac，可使用 Google Chrome 浏览器。 Windows 和 Mac 上也支持 Microsoft Edge Chromium。

### <a name="roles"></a>是否需要通过角色来访问虚拟机？

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="pricingpage"></a>定价是多少？

有关详细信息，请参阅[定价页](https://aka.ms/BastionHostPricing)。

### <a name="session"></a>为什么在 Bastion 会话启动前收到了“你的会话已过期”的错误消息？

会话只能从 Azure 门户启动。 登录到 Azure 门户，并重新开始会话。 如果直接从另一个浏览器会话或选项卡转到 URL，则会出现此错误。 它有助于确保会话更安全，并且该会话只能通过 Azure 门户来访问。

### <a name="keyboard"></a>Bastion 远程会话期间支持哪些键盘布局？

Azure Bastion 目前在 VM 内支持 en-us-qwerty 键盘布局。  对其他区域设置的键盘布局的支持尚在开发中。

### <a name="udr"></a>Azure Bastion 子网是否支持用户定义的路由 (UDR)？

不是。 Azure Bastion 子网不支持 UDR。
对于在同一虚拟网络中同时包含 Azure Bastion 和 Azure 防火墙/网络虚拟设备 (NVA) 的方案，无需强制流量从 Azure Bastion 子网发往 Azure 防火墙，因为 Azure Bastion 与 VM 之间的通信是专用的。 有关更多详细信息，请参阅[通过 Bastion 访问 Azure 防火墙后的 VM](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)。

### <a name="filetransfer"></a>Azure Bastion RDP 会话是否支持文件传输？

我们正在努力添加新功能。 目前还不支持文件传输，但它是我们路线图的一部分。 请随时在 [Azure Bastion 反馈页](https://feedback.azure.com/forums/217313-networking?category_id=367303)上分享有关新功能的反馈。
