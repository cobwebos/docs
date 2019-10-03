---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361539"
---
### <a name="bastion-tier"></a>堡垒层

堡垒主机是一个可选组件, 你可以将其用作跳转服务器来访问应用程序和数据库实例。 可以为堡垒主机 VM 分配公共 IP 地址, 但建议使用本地网络设置 ExpressRoute 连接或站点到站点 VPN, 以便进行安全访问。 此外, 只应为传入流量打开 SSH (端口22、Linux) 或 RDP (端口3389、Windows Server)。 为实现高可用性, 请在两个可用性区域或单个可用性集中部署一个堡垒主机。

你还可以在 Vm 上启用 SSH 代理转发, 这允许你通过从堡垒主机转发凭据来访问虚拟网络中的其他 Vm。 或者使用 SSH 隧道来访问其他实例。

下面是代理转发的示例:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

此命令连接到堡垒, 然后立即再次运行`ssh` , 因此你可以获得目标实例上的终端。 如果群集配置不同, 则可能需要在目标实例上指定非 root 用户。 `-A`参数转发代理连接, 以便自动使用本地计算机上的私钥。 请注意, 代理转发是一个链, 因此第`ssh`二个命令`-A`还包括, 以便从目标实例启动的所有后续 SSH 连接也使用本地私钥。