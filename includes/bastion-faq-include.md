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
ms.openlocfilehash: 4f49220da5d996615c9f8ef7cad2b6c6793866b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466281"
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
