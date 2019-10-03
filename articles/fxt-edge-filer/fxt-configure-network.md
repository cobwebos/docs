---
title: 调整 Microsoft Azure FXT Edge Filer 群集的网络设置
description: 如何在创建 Azure FXT Edge Filer 群集后自定义网络设置
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 36ed354304cb1c88e48088f4b36c1ad0350af0dc
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543019"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>教程：配置群集的网络设置 

在使用新创建的 Azure FXT Edge Filer 群集之前，应检查并自定义工作流的多个网络设置。 

本教程介绍可能需要针对新群集进行调整的网络设置。 

将学习以下内容： 

> [!div class="checklist"]
> * 创建群集后可能需要更新哪些网络设置
> * 哪些 Azure FXT Edge Filer 用例需要 AD 服务器或 DNS 服务器 
> * 如何配置轮循 DNS (RRDNS) 以自动对发送到 FXT 群集的客户请求进行负载均衡

完成这些步骤所需的时间取决于需要在系统中进行多少项配置更改：

* 如果只需阅读本教程并检查一些设置，则需要 10-15 分钟。 
* 如果需要配置轮循 DNS，该任务将需要 1 小时或以上。

## <a name="adjust-network-settings"></a>调整网络设置

设置新的 Azure FXT Edge Filer 群集时需要完成多个网络相关任务。 请检查此列表并确定哪些项适用于你的系统。

要详细了解群集的网络设置，请阅读群集配置指南中的[配置网络服务](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html)。

* 为面向客户端的网络配置轮询 DNS（可选）

  根据[为 FXT Edge Filer 群集配置 DNS](#configure-dns-for-load-balancing) 中所述配置 DNS 系统，对群集流量进行负载均衡。

* 验证 NTP 设置

* 配置 Active Directory 和用户名/组名下载（如果需要）

  如果网络主机使用 Active Directory 或另一种外部目录服务，则必须修改群集的目录服务配置，设置群集如何下载用户名和组信息。 有关详细信息，请阅读群集配置指南中的“群集” > “目录服务”   。

  如果需要 SMB 支持，则需要 AD 服务器。 请在开始设置 SMB 之前配置 AD。

* 定义 VLAN（可选）
  
  请在定义群集的 vserver 和全局命名空间之前，配置所需的任何其他 VLAN。 有关详细信息，请阅读群集配置指南中的[使用 VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview)。

* 配置代理服务器（如果需要）

  如果群集使用代理服务器来访问外部地址，请执行以下步骤进行设置：

  1. 在“代理配置”设置页面中定义代理服务器 
  1. 将代理服务器配置应用于“群集” > “常规设置”页面或“Core Filer 详细信息”页面    。
  
  有关详细信息，请阅读群集配置指南中的[使用 Web 代理](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html)。

* 上传[加密证书](#encryption-certificates)供群集使用（可选）

### <a name="encryption-certificates"></a>加密证书

FXT Edge Filer 群集使用 X.509 证书执行以下功能：

* 加密群集管理流量

* 代表客户端向第三方 KMIP 服务器进行身份验证

* 验证云提供商的服务器证书

如果需要将证书上传到群集，请使用“群集” > “证书”设置页面   。 有关详细信息，请参阅群集配置指南中的[群集 > 证书](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html)页面。

要加密群集管理通信，请使用“群集” > “常规设置”设置页面，选择用于管理 SSL 的证书   。

> [!Note] 
> 云服务访问密钥使用“云凭据”配置页面进行保存  。 上文的[添加 Core Filer](fxt-add-storage.md#add-a-core-filer) 部分显示了示例；有关详细信息，请参阅群集配置指南中的[云凭据](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html)部分。 

## <a name="configure-dns-for-load-balancing"></a>将 DNS 配置为负载均衡

本部分介绍将轮循 DNS (RRDNS) 系统配置为在 FXT Edge Filer 群集的所有面向客户端的 IP 地址之间分发客户端负载的基础知识。 

### <a name="decide-whether-or-not-to-use-dns"></a>决定是否使用 DNS

建议始终配置负载均衡，但无需始终使用 DNS。 例如，对于某些类型的客户端工作流，使用脚本在装载群集时在客户端之间均匀分配群集 IP 地址可能更有意义。 [装载群集](fxt-mount-clients.md)中介绍了几种方法。 

在决定是否使用 DNS 服务器时，请记住以下事项： 

* 如果你的系统只能通过 NFS 客户端访问，则不需要 DNS。 可以使用数字 IP 地址指定所有网络地址。 

* 如果你的系统支持 SMB (CIFS) 访问，则需要 DNS，因为必须为 Active Directory 服务器指定 DNS 域。

* 如果要使用 Kerberos 身份验证，则需要 DNS。

### <a name="round-robin-dns-configuration-details"></a>轮循 DNS 配置详细信息

当客户端访问群集时，RRDNS 会自动在所有可用接口之间平衡其请求。

为获得最佳性能，请配置 DNS 服务器以处理面向客户端的群集地址，如下图所示。

左侧显示群集虚拟服务器，IP 地址显示在右侧中间。 如图所示，使用 A 记录和指针配置每个客户端接入点。

![群集轮询 DNS 图 - 图片的详细替换文字说明](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[详细文字说明](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

### <a name="enable-dns-in-the-cluster"></a>在群集中启用 DNS 

在“群集” > “管理网络”设置页面中指定群集使用的 DNS 服务器   。 该页面上的设置包括：

* DNS 服务器地址
* DNS 域名
* DNS 搜索域

有关更多详细信息，请阅读群集配置指南中的 [DNS 设置](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)。

## <a name="next-steps"></a>后续步骤

这是 Azure FXT Edge Filer 群集的最后一个基本配置步骤。 

* 在[监视硬件状态](fxt-monitor.md)中了解系统的 LED 和其他指示。
* 在[装载群集](fxt-mount-clients.md)中了解客户端应如何装载 FXT Edge Filer 群集。 
* 有关操作和管理 FXT Edge Filer 群集的详细信息，请参阅[群集配置指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)。 