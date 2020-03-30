---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361539"
---
### <a name="bastion-tier"></a>堡垒层

堡垒主机是一个可选组件，您可以将其用作跳转服务器来访问应用程序和数据库实例。 堡垒主机 VM 可以为其分配公共 IP 地址，但建议使用本地网络设置 ExpressRoute 连接或站点到站点 VPN 以进行安全访问。 此外，应仅打开 SSH（端口 22、Linux）或 RDP（端口 3389，Windows 服务器）才能用于传入流量。 为获得高可用性，请将堡垒主机部署在两个可用性区域或单个可用性集中。

您还可以在 VM 上启用 SSH 代理转发，这允许您通过从堡垒主机转发凭据来访问虚拟网络中的其他 VM。 或者，使用 SSH 隧道访问其他实例。

下面是代理转发的示例：

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

此命令连接到堡垒，然后立即再次运行`ssh`，因此您可以在目标实例上获取终端。 如果群集配置不同，则可能需要在目标实例上指定 root 以外的用户。 参数`-A`转发代理连接，以便自动使用本地计算机上的私钥。 请注意，代理转发是一个链，因此第二`ssh`个命令还包括`-A`以便从目标实例启动的任何后续 SSH 连接也使用本地私钥。