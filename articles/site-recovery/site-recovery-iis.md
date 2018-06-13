---
title: 使用 Azure Site Recovery 复制基于 IIS 的多层 Web 应用程序 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 复制 IIS Web 场虚拟机。
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 1ed0184ac76b5fb3e607458559327da5e8fe90c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
ms.locfileid: "29809414"
---
# <a name="replicate-a-multi-tier-iis-based-web-application"></a>复制基于 IIS 的多层 Web 应用程序

应用程序软件是组织中业务生产力的引擎。 各种 Web 应用程序可在组织中发挥不同的作用。 某些应用程序，例如工资单处理应用程序、财务应用程序和面向客户的网站，对于组织而言可能至关重要。 组织必须不间断地运转这些应用程序以防止工作中断。 更重要的是，使这些应用程序保持连续运行有助于防止组织的品牌形象受到损害。

关键的 Web 应用程序通常设置为多层应用程序，其 Web、数据库和应用程序分别位于不同的层。 除了分散在不同的层以外，应用程序还可以在每个层中使用多个服务器来对流量进行负载均衡。 此外，各个层之间以及 Web 服务器上的映射可以基于静态 IP 地址。 故障转移时，其中的某些映射需要更新，尤其是在 Web 服务器上配置了多个网站时。 如果 Web 应用程序使用 SSL，则必须更新证书绑定。

不是以复制为基础的传统恢复方法涉及到备份各种配置文件、注册表设置、绑定、自定义组件（COM 或 .NET）、内容和证书。 此外，需要通过一系列手动步骤恢复文件。 传统的文件备份和手动恢复方法非常繁琐、容易出错且没有弹性。 例如，我们经常忘记备份证书。 故障转移后，我们别无他法，只能为服务器购买新证书。

良好的灾难恢复解决方案支持对复杂应用程序体系结构的恢复计划建模。 此外，我们还能在恢复计划中添加自定义步骤，用于处理各个层级之间的应用程序映射。 如果发生灾难，只需按一下鼠标，应用程序映射就能彻底解决问题，帮助降低 RTO。

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 保护基于 Internet Information Services (IIS) 的 Web 应用程序。 内容包括如何将基于 IIS 的三层 Web 应用程序复制到 Azure、如何执行灾难恢复演练，以及如何将应用程序故障转移到 Azure 的最佳做法。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保知道如何执行以下任务：

* [将虚拟机复制到 Azure](vmware-azure-tutorial.md)
* [设计恢复网络](site-recovery-network-design.md)
* [执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)
* [执行到 Azure 的故障转移](site-recovery-failover.md)
* [复制域控制器](site-recovery-active-directory.md)
* [复制 SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>部署模式
基于 IIS 的 Web 应用程序通常遵循以下部署模式之一：

**部署模式 1**

包含应用程序请求路由 (ARR)、IIS 服务器和 SQL Server 的基于 IIS 的 Web 场。

![包含三个层的基于 IIS 的 Web 场示意图](./media/site-recovery-iis/deployment-pattern1.png)

**部署模式 2**

包含 ARR、IIS 服务器、应用程序服务器和 SQL Server 的基于 IIS 的 Web 场。

![包含四个层的基于 IIS 的 Web 场示意图](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery 支持

本文中的示例在 Windows Server 2012 R2 Enterprise 上使用装有 IIS 7.5 的 VMware 虚拟机。 由于 Site Recovery 复制不区分应用程序，因此本文中的建议应该也适用于下表中所列的方案以及不同版本的 IIS。

### <a name="source-and-target"></a>源和目标

方案 | 到辅助站点 | 到 Azure
--- | --- | ---
Hyper-V | 是 | 是
VMware | 是 | 是
物理服务器 | 否 | 是
Azure|不可用|是

## <a name="replicate-virtual-machines"></a>复制虚拟机

若要开始将所有 IIS Web 场虚拟机复制到 Azure，请遵照[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)中的指导。

如果使用的是静态 IP，可以指定希望虚拟机采用的 IP 地址。 若要设置 IP 地址，请转到“计算和网络设置” > “目标 IP”。

![演示如何在 Site Recovery 的“计算和网络”窗格中设置目标 IP 的屏幕截图](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>创建恢复计划
恢复计划支持在故障转移期间将多层应用程序中的各个层排序。 排序有助于保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请完成[使用 Site Recovery 创建恢复计划](site-recovery-create-recovery-plans.md)中所述的步骤。

### <a name="add-virtual-machines-to-failover-groups"></a>将虚拟机添加到故障转移组
典型的多层 IIS Web 应用程序由以下组件构成：
* 包含 SQL 虚拟机的数据库层。
* 由 IIS 服务器和应用程序层构成的 Web 层。 

根据层将虚拟机添加到不同的组中：

1. 创建恢复计划。 在组 1 下面添加数据库层虚拟机。 这可以确保最后关闭、最先启动数据库层虚拟机。
1. 在组 2 下面添加应用程序层虚拟机。 这可以确保在启动数据库层后启动应用程序层虚拟机。
1. 在组 3 下面添加 Web 层虚拟机。 这可以确保在启动应用程序层后启动 Web 层虚拟机。
1. 在组 4 下面添加负载均衡虚拟机。 这可以确保在启动 Web 层后启动负载均衡虚拟机。

有关详细信息，请参阅[自定义恢复计划](site-recovery-runbook-automation.md#customize-the-recovery-plan)。


### <a name="add-a-script-to-the-recovery-plan"></a>将脚本添加到恢复计划
在故障转移后或测试故障转移期间，可能需要在 Azure 虚拟机上执行一些操作才能让 IIS Web 场正常工作。 可将某些故障转移后的操作自动化。 例如，可在恢复计划中添加相应的脚本，来更新 DNS 条目、更改站点绑定或更改连接字符串。 [将 VMM 脚本添加到恢复计划](site-recovery-how-to-add-vmmscript.md)介绍了如何使用脚本来设置自动化任务。

#### <a name="dns-update"></a>DNS 更新
如果为动态 DNS 更新配置了 DNS，则虚拟机在启动时，通常会使用新的 IP 地址更新 DNS。 如果想要添加一个明确的步骤来使用虚拟机的新 IP 地址更新 DNS，请添加这个[用于更新 DNS 中的 IP 的脚本](https://aka.ms/asr-dns-update)，作为恢复计划组中的故障转移后操作。  

#### <a name="connection-string-in-an-applications-webconfig"></a>应用程序 web.config 中的连接字符串
连接字符串指定与网站通信的数据库。 如果连接字符串包含数据库虚拟机的名称，则故障转移后无需进行其他步骤。 应用程序可自动与数据库通信。 此外，如果数据库虚拟机的 IP 地址得到保留，则不需要更新连接字符串。 

如果连接字符串指向使用某个 IP 地址的数据库虚拟机，则故障转移后需要更新该字符串。 例如，以下连接字符串指向 IP 地址为 127.0.1.2 的数据库：

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

若要更新 Web 层中的连接字符串，可以通过恢复计划中的“组 3”后面添加 [IIS 连接更新脚本](https://aka.ms/asr-update-webtier-script-classic)。

#### <a name="site-bindings-for-the-application"></a>应用程序的站点绑定
每个站点包含绑定信息。 绑定信息包括绑定类型、IIS 服务器侦听站点请求所用的 IP 地址、端口号和站点的主机名。 在故障转移期间，如果与这些绑定关联的 IP 地址发生更改，则可能需要更新这些绑定。

> [!NOTE]
>
> 如果将站点绑定设置为“全部取消分配”，则故障转移后不需要更新此绑定。 此外，如果与站点关联的 IP 地址在故障转移后未发生未更改，则不需要更新站点绑定。 （能否保留 IP 地址取决于网络体系结构以及分配给主站点和恢复站点的子网。 因此，在组织中不一定能够使用绑定。）

![演示如何设置 SSL 绑定的屏幕截图](./media/site-recovery-iis/sslbinding.png)

如果将 IP 地址关联到了某个站点，请使用新 IP 地址更新所有站点绑定。 若要更改站点绑定，请在恢复计划中的“组 3”后面添加 [IIS Web 层更新脚本](https://aka.ms/asr-web-tier-update-runbook-classic)。

#### <a name="update-the-load-balancer-ip-address"></a>更新负载均衡器 IP 地址
如果使用 ARR 虚拟机，请在“组 4”后面添加 [IIS ARR 故障转移脚本](https://aka.ms/asr-iis-arrtier-failover-script-classic)来更新 IP 地址。

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>用于 HTTPS 连接的 SSL 证书绑定
网站可与 SSL 证书关联，帮助确保在 Web 服务器与用户浏览器之间实现安全通信。 如果网站有一个 HTTPS 连接，并且将一个 HTTPS 站点绑定关联到了具有 SSL 证书绑定的 IIS 服务器的 IP 地址，则故障转移后，必须使用 IIS 虚拟机的 IP 地址来为证书添加新的站点绑定。

可针对以下组件颁发 SSL 证书：

* 网站的完全限定域名。
* 服务器的名称。
* 域名的通配符证书。  
* IP 地址。 如果 SSL 证书是针对 IIS 服务器的 IP 地址颁发的，则需要针对 Azure 站点上 IIS 服务器的 IP 地址颁发另一个 SSL 证书。 需要为此证书创建另一个 SSL 绑定。 因此，我们建议不要使用针对 IP 地址颁发的 SSL 证书。 此选项不太常用，根据新证书颁发机构/浏览器论坛中所述的更改，此选项即将停用。

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>更新 Web 层与应用层之间的依赖关系
如果存在基于虚拟机 IP 地址的应用程序特定依赖关系，则故障转移后必须更新此依赖关系。

## <a name="run-a-test-failover"></a>运行测试故障转移

1. 在 Azure 门户中，选择恢复服务保管库。
2. 选择针对 IIS Web 场创建的恢复计划。
3. 选择“测试故障转移”。
4. 若要启动测试故障转移过程，请选择恢复点和 Azure 虚拟网络。
5. 当辅助环境启动时，可以执行验证。
6. 完成验证后，选择“验证完成”可清理测试故障转移环境。

有关详细信息，请参阅[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>运行故障转移

1. 在 Azure 门户中，选择恢复服务保管库。
1. 选择针对 IIS Web 场创建的恢复计划。
1. 选择“故障转移”。
1. 若要启动故障转移过程，请选择恢复点。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。

## <a name="next-steps"></a>后续步骤
* 详细了解如何使用 Site Recovery [复制其他应用程序](site-recovery-workload.md)。
