---
title: Avere vFXT DNS - Azure
description: 使用 Avere vFXT for Azure 配置 DNS 服务器以进行轮询负载均衡
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 11ff310dae3c4733283d965a518df42a0711ce01
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75416043"
---
# <a name="avere-cluster-dns-configuration"></a>Avere 群集 DNS 配置

本节介绍配置 DNS 系统以对 Avere vFXT 群集进行负载均衡的基础知识。

本文档不包含有关在 Azure 环境中设置和管理 DNS 服务器的说明。

不要使用轮询 DNS 对 Azure 中的 vFXT 群集进行负载均衡，而是考虑使用手动方法在客户端装载时均匀分配 IP 地址。 [装载 Avere 群集](avere-vfxt-mount-clients.md)中描述了几种方法。

在决定是否使用 DNS 服务器时，请记住以下事项：

* 如果只能通过 NFS 客户端访问系统，则无需使用 DNS，可使用数字 IP 地址指定所有网络地址。

* 如果你的系统支持 SMB (CIFS) 访问，则需要 DNS，因为必须为 Active Directory 服务器指定 DNS 域。

* 如果要使用 Kerberos 身份验证，则需要 DNS。

## <a name="load-balancing"></a>负载平衡

要分发总体负载，请将 DNS 域配置为对面向客户端的 IP 地址使用轮询负载分配。

## <a name="configuration-details"></a>配置详细信息

当客户端访问群集时，RRDNS 会自动在所有可用接口之间平衡其请求。

为获得最佳性能，请配置 DNS 服务器以处理面向客户端的群集地址，如下图所示。

左侧显示群集虚拟服务器，IP 地址显示在右侧中间。 如图所示，使用 A 记录和指针配置每个客户端接入点。

![Avere 群集轮循机制 DNS 关系图](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

每个面向客户端的 IP 地址必须具有唯一的名称供群集内部使用。 （在此图中，为了清楚起见，将客户端 IP 命名为 vs1-client-IP- *，但在生产中，应使用更简洁的名称，如 client*。）

客户端使用 vserver 名称作为服务器参数来装载群集。

修改 DNS 服务器的 ``named.conf`` 文件，以设置查询到 vserver 的循环顺序。 此选项可确保循环显示所有可用值。 添加如下语句：

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

以下 ``nsupdate`` 命令提供了正确配置 DNS 的示例：

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>群集 DNS 设置

在“群集” > “管理网络设置”页面中指定 vFXT 群集使用的 DNS 服务器。 该页面上的设置包括：

* DNS 服务器地址
* DNS 域名
* DNS 搜索域

有关使用此页面的详细信息，请阅读“Avere 群集配置指南”中的 [DNS 设置](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)。
