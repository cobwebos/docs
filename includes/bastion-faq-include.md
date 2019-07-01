---
title: include 文件
description: include 文件
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e29a9265e010c3f442b742faf62b16dae02739fa
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191136"
---
### <a name="preview"></a>如何参与公共预览版？

需要先加入，然后才能参与公共预览版。 请按照[此文](../articles/bastion/bastion-create-host-portal.md)中的步骤创建新的 Azure Bastion 资源。 当前，在访问和使用此服务时，必须使用 [Azure 门户 - 预览版](https://aka.ms/BastionHost)而不是常规的 Azure 门户。

### <a name="regions"></a>在预览版期间，面向哪些区域提供？

可通过 [Azure 门户 - 预览版链接](https://aka.ms/BastionHost)在下列任一预览区域中部署和使用 Bastion 资源。

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>在 Azure 门户中找不到 Bastion 资源， 我该怎么办？

确保使用 [Azure 门户 - 预览版链接](https://aka.ms/BastionHost)，而不是常规的 Azure 门户。

### <a name="publicip"></a>我是否需要在虚拟机上有一个公共 IP？

不需要在使用 Azure Bastion 服务连接到的 Azure 虚拟机上有一个公共 IP。 Bastion 服务会通过虚拟网络中的虚拟机的专用 IP 打开到虚拟机的 RDP/SSH 会话/连接。

### <a name="rdpssh"></a>是否需要 RDP 或 SSH 客户端？

无需 RDP 或 SSH 客户端即可在 Azure 门户中访问 RDP/SSH 来连接到 Azure 虚拟机。 使用 [Azure 门户 - 预览版链接](https://aka.ms/BastionHost)访问门户的外部预览测试版。 这样就能够直接在浏览器中通过 RDP/SSH 来访问虚拟机。

### <a name="agent"></a>是否需要在 Azure 虚拟机中运行代理？

无需在浏览器或 Azure 虚拟机上安装代理或任何软件。 Bastion 服务没有代理，不需要任何其他软件即可使用 RDP/SSH。

### <a name="browsers"></a>支持哪些浏览器？

在公共预览版期间，可使用 Windows 上的 Microsoft Edge 浏览器或 Google Chrome。 对于 Apple Mac，可使用 Google Chrome 浏览器。 Windows 和 Mac 上也支持 Microsoft Edge Chromium。

### <a name="roles"></a>是否需要通过角色来访问虚拟机？

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="previewbill"></a>定价 - 我加入此预览计划是否需要支付费用？

在公共预览期间，只需支付部分费用。 不过，部署没有附加 SLA。 有关详细信息，请参阅[定价页](https://aka.ms/BastionHostPricing)。