---
title: "使用 Azure Site Recovery 复制基于 IIS 的多层 Web 应用程序 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 复制 IIS Web 场虚拟机。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: nisoneji
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b23624fc7e82af1cb593a1aedd138ae0d6637ae7
ms.contentlocale: zh-cn
ms.lasthandoff: 03/29/2017


---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>使用 Azure Site Recovery 复制基于 IIS 的多层 Web 应用程序

## <a name="overview"></a>概述


应用程序软件是组织中业务生产力的引擎。 各种 Web 应用程序可在组织中发挥不同的作用。 其中的某些应用程序，例如工资单处理系统、财务应用程序和面向客户的网站，对于组织而言可能至关重要。 组织必须全天候运转这些应用程序以防止工作中断，更重要的是，防止组织的品牌形象受到损害。

关键的 Web 应用程序通常设置为多层应用程序，其 Web、数据库和应用程序分别位于不同的层。 除了分散在不同的层以外，应用程序还可以在每个层中使用多个服务器来对流量进行负载均衡。 此外，各个层之间以及 Web 服务器上的映射可以基于静态 IP 地址。 故障转移时，其中的某些映射需要更新，尤其是在 Web 服务器上配置了多个网站时。 如果 Web 应用程序使用 SSL，则需要更新证书绑定。

不是以复制为基础的传统恢复方法涉及到备份各种配置文件、注册表设置、绑定、自定义组件（COM 或 .NET）、内容和证书，以及通过一系列手动步骤恢复文件。 很明显，这些方法非常繁琐、容易出错且没有弹性。 例如，我们经常忘记备份证书。如果出现这种情况，在故障转移后，我们别无他法，只能为服务器购买新证书。

合理的灾难恢复解决方案应该允许围绕上述复杂应用程序体系结构为恢复计划建模，同时，可以添加自定义的步骤来处理各层之间的应用程序映射，以便在发生导致 RTO 降低的灾难时，只需按一下鼠标就能彻底解决问题。


本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 保护基于 IIS 的 Web 应用程序。 内容包括如何将基于 IIS 的三层 Web 应用程序复制到 Azure、如何执行灾难恢复演练，以及如何将应用程序故障转移到 Azure 的最佳做法。


## <a name="prerequisites"></a>先决条件

在开始之前，请确保了解以下知识：

1. [将虚拟机复制到 Azure](site-recovery-vmware-to-azure.md)
1. 如何[设计恢复网络](site-recovery-network-design.md)
1. [执行到 Azure 的测试故障转移](./site-recovery-test-failover-to-azure.md)
1. [执行到 Azure 的故障转移](site-recovery-failover.md)
1. 如何[复制域控制器](site-recovery-active-directory.md)
1. 如何[复制 SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>部署模式
基于的 IIS Web 应用程序通常遵循以下部署模式之一：

**部署模式 1**：包含应用程序请求路由 (ARR)、IIS 服务器和 Microsoft SQL Server 的基于 IIS 的 Web 场。

![部署模式](./media/site-recovery-iis/deployment-pattern1.png)

**部署模式 2**：包含应用程序请求路由 (ARR)、IIS 服务器、应用程序服务器和 Microsoft SQL Server 的基于 IIS 的 Web 场。


![部署模式](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery 支持

本文在 Windows Server 2012 R2 Enterprise 中使用装有 IIS 服务器版本 7.5 的 VMware 虚拟机。 由于 Site Recovery 复制不区分应用程序，因此本文提供的建议应该也适用于后续方案以及不同版本的 IIS。

### <a name="source-and-target"></a>源和目标

**方案** | **到辅助站点** | **到 Azure**
--- | --- | ---
**Hyper-V** | 是 | 是
**VMware** | 是 | 是
**物理服务器** | 否 | 是

## <a name="replicate-virtual-machines"></a>复制虚拟机

遵循[此指南](site-recovery-vmware-to-azure.md)开始将所有 IIS Web 场虚拟机复制到 Azure。

如果使用的是静态 IP，请在“计算和网络”设置的[**目标 IP**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) 设置中指定希望虚拟机采用的 IP。

![目标 IP](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>创建恢复计划

使用恢复计划可将多层应用程序中各个层的故障转移排序，从而可以保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请遵循以下步骤。  [详细了解如何创建恢复计划](./site-recovery-create-recovery-plans.md)。

### <a name="adding-virtual-machines-to-failover-groups"></a>将虚拟机添加到故障转移组
典型的多层 IIS Web 应用程序包括：包含 SQL 虚拟机的数据库层、由 IIS 服务器构成的 Web 层，以及应用程序层。 请按如下所示，根据不同的层将所有这些虚拟机添加到不同的组中。 [详细了解如何自定义恢复计划](site-recovery-runbook-automation.md#customize-the-recovery-plan)。

1. 创建恢复计划。 在“组 1”下面添加数据库层虚拟机，确保最后关闭、最先启动这些虚拟机。

1. 在“组 2”下面添加应用程序层虚拟机，以便在启动数据库层后启动这些虚拟机。

1. 在“组 3”下面添加 Web 层虚拟机，以便在启动应用程序层后启动这些虚拟机。

1. 在“组 4”下面添加负载均衡层虚拟机，以便在启动 Web 层后启动这些虚拟机。


### <a name="adding-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划
在故障转移/测试故障转移后，可能需要在 Azure 虚拟机上执行一些操作才能让 IIS Web 场正常工作。 可按如下所示在恢复计划中添加相应的脚本，将更新 DNS 条目、更改站点绑定、更改连接字符串等故障转移后操作自动化。 [详细了解如何添加脚本恢复计划](./site-recovery-create-recovery-plans.md#add-scripts)。

#### <a name="dns-update"></a>DNS 更新
如果为动态 DNS 更新配置了 DNS，则虚拟机在启动后，通常会使用新的 IP 更新 DNS。 如果想要添加一个明确的步骤来使用虚拟机的新 IP 更新 DNS，请添加这个[用于更新 DNS 中的 IP 的脚本](https://aka.ms/asr-dns-update)，作为恢复计划组中的后期操作。  

#### <a name="connection-string-in-an-applications-webconfig"></a>应用程序 web.config 中的连接字符串
连接字符串指定与网站通信的数据库。

如果连接字符串附带了数据库虚拟机的名称，则故障转移后不需要执行其他步骤，应用程序可自动连接到数据库。 此外，如果数据库虚拟机的 IP 地址得到保留，则不需要更新连接字符串。 如果连接字符串指向使用某个 IP 地址的数据库虚拟机，则故障转移后需要更新该字符串。 例如 以下连接字符串指向 IP 为 127.0.1.2 的数据库

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

在 Web 层中，可以通过恢复计划的“组 3”后面的添加 [IIS 连接更新脚本](https://aka.ms/asr-update-webtier-script-classic)来更新连接字符串。

#### <a name="site-bindings-for-the-application"></a>应用程序的站点绑定
每个站点包含绑定信息，其中包括绑定类型、IIS 服务器侦听站点请求所用的 IP 地址、端口号和站点的主机名。 故障转移时，如果与这些绑定关联的 IP 地址发生更改，则可能需要更新这些绑定。

> [!NOTE]
>
> 如果按以下示例中所示为站点绑定标记了“全部取消分配”，则故障转移后不需要更新此绑定。 此外，如果与站点关联的 IP 地址在故障转移后未发生未更改，则不需要更新站点绑定（能否保留 IP 地址取决于网络体系结构以及分配给主站点和恢复站点的子网，因此不一定适用于你的组织。）

![SSL 绑定](./media/site-recovery-iis/sslbinding.png)

如果将 IP 地址关联到了某个站点，需要使用新 IP 地址更新所有站点绑定。 可在恢复计划中的“组 3”后面添加 [IIS Web 层更新脚本](https://aka.ms/asr-web-tier-update-runbook-classic)来更改站点绑定。


#### <a name="update-load-balancer-ip-address"></a>更新负载均衡器 IP 地址
如果使用应用程序请求路由虚拟机，请在“组 4”后面添加 [IIS ARR 故障转移脚本](https://aka.ms/asr-iis-arrtier-failover-script-classic)来更新 IP 地址。

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>用于 https 连接的 SSL 证书绑定
网站可与 SSL 证书关联，帮助确保在 Web 服务器与用户浏览器之间实现安全通信。 如果网站有一个 https 连接，并且将一个 https 站点绑定关联到了具有 SSL 证书绑定的 IIS 服务器的 IP 地址，则故障转移后，需要使用 IIS 虚拟机的 IP 来为证书添加新的站点绑定。

可针对以下对象颁发 SSL 证书 -

a) 网站的完全限定域名<br>
b) 服务器的名称<br>
c) 域名的通配符证书<br>
d) IP 地址 – 如果 SSL 证书是针对 IIS 服务器的 IP 颁发的，则需要针对 Azure 站点上 IIS 服务器的 IP 地址颁发另一个 SSL 证书，并需要为此证书创建另一个 SSL 绑定。 因此，建议不要使用针对 IP 颁发的 SSL 证书。 此选项不太常用，根据新 CA/浏览器论坛中所述的更改，此选项即将停用。

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>更新 Web 层与应用程序层之间的依赖关系
如果存在基于虚拟机 IP 地址的应用程序特定依赖关系，则故障转移后需要更新此依赖关系。

## <a name="doing-a-test-failover"></a>执行测试故障转移
遵循[此指南](site-recovery-test-failover-to-azure.md)执行测试故障转移。

1.  转到 Azure 门户并选择你的恢复服务保管库。
1.  单击针对 IIS Web 场创建的恢复计划。
1.  单击“测试故障转移”。
1.  选择恢复点和 Azure 虚拟网络开始测试故障转移过程。
1.  辅助环境启动后，可以执行验证。
1.  完成验证后，可以选择“验证完成”，随后将清理测试故障转移环境。

## <a name="doing-a-failover"></a>执行故障转移
执行故障转移时，请遵循[此指南](site-recovery-failover.md)。

1.  转到 Azure 门户并选择你的恢复服务保管库。
1.  单击针对 IIS Web 场创建的恢复计划。
1.  单击“故障转移”。
1.  选择恢复点开始故障转移过程。

## <a name="next-steps"></a>后续步骤
详细了解如何使用 Site Recovery [复制其他应用程序](site-recovery-workload.md)。

