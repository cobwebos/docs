---
title: 在 Azure VMware 解决方案上部署地平线
description: 了解如何在 Azure VMware 解决方案上部署 VMware 地平线。
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 60207b0ed9e1df805ac667752b55f14a693ec25c
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492415"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>在 Azure VMware 解决方案上部署地平线 

>[!NOTE]
>本文档重点介绍 VMware 地平线产品。 在产品名称从范围7更改为水平之前，此名称之前称为地平线7。 尽管有一些共享组件，但范围与 Azure 上的地平线云的解决方案不同。 Azure VMware 解决方案的主要优点包括：更简单的大小调整方法和 VMware Cloud Foundation 管理已集成到 Azure 门户中。

[VMware 地平线](https://www.vmware.com/products/horizon.html)®是在数据中心运行的虚拟机和应用程序平台，它通过简单的集中式管理提供。 它将虚拟机和应用程序提供给任何位置的任何设备上的最终用户。 通过范围，可以创建和代理连接到 Windows 虚拟桌面、Linux 虚拟桌面、远程桌面服务器 (RDS) 托管应用程序、桌面和物理计算机。

本文重点介绍如何在 Azure VMware 解决方案上部署地平线。 有关 VMware 范围的一般信息，请参阅地平线生产文档：

* [什么是 VMware 范围？](https://www.vmware.com/products/horizon.html)

* [详细了解 VMware 范围](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [范围参考体系结构](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

借助 Azure VMware 解决方案的地平线简介，现在 Azure 平台上有两个 (VDI) 解决方案的虚拟桌面基础结构。 下图汇总了高级别的主要区别。

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Azure VMware 解决方案上的范围和 Azure 上的范围云之间的差异" border="false":::

地平线8版本系列上的地平线2006及更高版本支持本地部署和 Azure VMware 解决方案部署。 本地支持的一些地平线功能，但在 Azure VMware 解决方案中却不支持。 还支持地平线生态系统中的其他产品。 有关信息，请参阅 [功能奇偶校验和互操作性](https://kb.vmware.com/s/article/80850)。

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>在混合云中部署地平线

当你使用横向 Cloud Pod 体系结构 (CPA) 将本地数据中心与 Azure 数据中心互连时，你可以在混合云环境中部署范围。 CPA 通常用于扩展你的部署、构建混合云，并提供冗余以实现业务连续性和灾难恢复。 若要深入了解 VMware 地平线业务连续性指南，请参阅 [扩展现有的地平线7环境](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)。

>[!IMPORTANT]
>CPA 不是延伸的部署;每个水平箱都是不同的，并且属于各个 pod 的所有连接服务器都需要位于单个位置，并从网络角度在同一广播域上运行。

与本地或私有数据中心一样，可以在 Azure VMware 解决方案私有云中部署范围。 在以下部分中，我们将讨论部署范围本地和 Azure VMware 解决方案的主要差异。

Azure 私有云在概念上与 VMware SDDC 相同，后者通常用于地平线文档中。 本文档的其余部分将使用术语 "Azure 私有云" 和 "VMware SDDC 可互换"。

若要将 Azure VMware 解决方案的范围用于管理订阅许可证，需要水平范围的云连接器。 可以在 Azure 虚拟网络和地平线连接服务器之间部署云连接器。

>[!IMPORTANT]
>目前尚不支持对 Azure VMware 解决方案的水平范围控制平面。 请确保下载地平线云连接器的 VHD 版本。

## <a name="vcenter-cloud-admin-role"></a>vCenter 云管理员角色

由于 Azure VMware 解决方案是一种 SDDC 服务，Azure 可管理 azure VMware 解决方案上的 SDDC 生命周期，因此，Azure VMware 解决方案上的 vCenter 权限模型受设计的限制。

客户需要使用云管理员角色，该角色具有有限的 vCenter 权限集。 已将地平线产品修改为与 Azure VMware 解决方案上的云管理员角色一起使用，具体如下：

* 即时克隆设置已修改为在 Azure VMware 解决方案上运行。

* 在 Azure VMware 解决方案上创建了一个特定的 vSAN 策略 (VMware_Horizon) ，该解决方案必须提供，并可用于为地平线部署的 SDDCs。

* 在 Azure VMware 解决方案上运行时，将禁用 vSphere 基于内容的读取缓存 (CBRC) ，也称为查看存储加速器。

>[!IMPORTANT]
>不能重新打开 CBRC。

>[!NOTE]
>只要部署水平 2006)  (（也就是水平8个分支），然后在 "水平连接服务器" 安装程序中选择 " **Azure** " 选项，Azure VMware 解决方案就会自动配置特定的水平设置。

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Azure VMware 解决方案部署体系结构的范围

典型的水平体系结构设计使用 pod 和块策略。 块是单个 vCenter，而多个块合并为一个 pod。 水平箱箱是按范围的可伸缩性限制确定的组织单位。 每个地平线箱都有一个单独的管理门户，因此标准设计做法是将 pod 数量降至最低。

每个云都有其自己的网络连接方案。 与 VMware SDDC 网络/NSX 边缘相结合，Azure VMware 解决方案网络连接为部署不同于本地的范围提供了独特的要求。

每个 Azure 私有云/SDDC 都能够处理4000桌面或应用会话，它们采用以下功能：

* 工作负载流量与 LoginVSI 任务辅助角色配置文件的通信。

* 仅考虑协议流量，无用户数据。

* NSX 边缘配置为大。

>[!NOTE]
>工作负载配置文件和需求可能不同，因此结果可能会因用例而异。 用户数据卷可能会降低工作负荷上下文中的规模限制。 调整大小并相应地计划部署。 有关详细信息，请参阅 [大小适用于地平线部署的 Azure VMware 解决方案主机的大小大小](#size-azure-vmware-solution-hosts-for-horizon-deployments) 准则部分。

由于 Azure 私有云/SDDC 最大限制，我们建议使用部署体系结构，在该体系结构中，水平连接服务器和 VMware 统一访问网关 (UAGs) 在 Azure 虚拟网络中运行。 这会有效地将每个 Azure 私有云/SDDC 变成一个块。 这进而使在 Azure VMware 解决方案上运行的地平线的可伸缩性最大化。

从 Azure 虚拟网络到 Azure 私有云/SDDCs 的连接应配置为 ExpressPath 快速路径。 下图显示了基本的地平线盒部署。

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Azure VMware 解决方案上的范围和 Azure 上的范围云之间的差异" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Azure VMware 解决方案的网络连接规模横向扩展

本部分详细介绍了在 Azure VMware 解决方案上横向扩展的网络体系结构，以及一些常见的部署示例。 这里的重点是关键网络元素。

### <a name="single-horizon-pd-on-azure-vmware-solution"></a>Azure VMware 解决方案上的单地平线 pd

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware 解决方案上的范围和 Azure 上的范围云之间的差异" border="false":::

单个地平线箱是最直接的部署方案。 在此示例中，你决定仅在美国东部区域部署一个地平线的 pod。 由于每个私有云/SDDC 预计大约要处理4000桌面会话的流量，因此，若要部署最大水平的 pod 大小，可以规划最多三个私有云/SDDCs 的部署。

因此，在此示例中，在 Azure 虚拟网络中部署)  (Vm 的集中基础结构虚拟机，可以根据工作负荷和数据需求，访问每个范围 pod 的12000会话。 每个私有云与 SDDC 到 Azure 虚拟网络之间的连接是 ExpressRoute 快速路径，因此不需要私有云之间的任何东部流量。

此基本部署示例的关键假设包括：

* 你没有需要使用 Cloud Pod 体系结构 (CPA) 连接到此新 pod 的本地地平线箱。

* 最终用户通过 internet 连接到其虚拟桌面 (与通过本地数据中心进行连接) 。

在此基本示例中，可以通过 VPN 或 ExpressRoute 线路将 Azure 虚拟网络中的 AD 域控制器与本地 Active Directory 连接。

下面讨论的基本示例的变化可能是为了支持本地资源的连接。 这可能是用户访问桌面和生成虚拟桌面应用程序流量，或使用 CPA 连接到本地地平线盒。

下图显示了如何执行此操作。若要将企业网络连接到 Azure 虚拟网络，需要 ExpressRoute。还需要使用 Global Reach 将公司网络与每个私有云/SDDCs 连接，这允许从 SDDC 连接到 ExpressRoute 和本地资源。

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Azure VMware 解决方案上的范围和 Azure 上的范围云之间的差异" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Azure VMware 解决方案上跨多个区域的多个地平线箱

对于另一个地平线箱示例，让我们看一下显示跨多个 pod 横向扩展的示例。在此示例中，你将在两个不同的区域部署两个地平线箱，并使用 CPA 来联合它们。网络配置与前面的示例类似，还有一些其他跨区域的链接。 

需要将每个区域的 Azure 虚拟网络流入量连接到其他区域中的私有云/SDDCs，允许作为 CPA 联合的一部分的水平连接服务器连接到管理下的所有桌面。将额外的私有云/SDDCs 添加到此配置，可以扩展到全部24000会话。 

尽管此示例显示了多个区域，如果要在同一区域中部署两个水平盒，则会应用相同的原则。 请注意，需要确保第二个地平线盒部署在 *单独的 Azure 虚拟网络*中。最后，正如你在前一个 Pod 示例中所做的那样，可以使用客户 ExpressRoute 和 Global Reach 将企业网络和本地 Pod 连接到此多 Pod/区域示例。

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware 解决方案上的范围和 Azure 上的范围云之间的差异" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>为地平线部署的 Azure VMware 解决方案主机调整大小 

在 Azure VMware 解决方案中运行的主机上，地平线大小的大小调整方法比本地范围更简单，因为 Azure VMware 解决方案主机实例已标准化。 准确的主机大小有助于确定支持 VDI 要求所需的主机数量，并是确定每个桌面的成本的核心。

### <a name="azure-vmware-solution-host-instance"></a>Azure VMware 解决方案主机实例

* PowerEdge R640 服务器-DSS 限制

* 36核心 \@ 2.3 ghz

* 576-GB RAM

* HBA330 12 Gbps SAS HBA 控制器 (非 RAID) 

* 1.92 TB SSD SATA 混合使用热插拔 AG 驱动器中的 6 Gbps 512 2.5，3 DWPD，10512 TBW

* Intel 1.6 TB，NVMe，混合使用 Express 闪存，2.5 SFF 驱动器，U. 2，P4600

* 2 vSAN 磁盘组： 1.6 x 4 (1.92 TB) 

### <a name="horizon-sizing-inputs"></a>地平线大小调整输入

了解以下计划工作负荷：

* 并发桌面数

* 每个桌面所需的 vCPU

* 每个桌面所需的 vRAM

* 每个桌面所需的存储

通常，VDI 部署会受到 CPU 或 RAM 的限制，因为这些因素决定了主机大小。 下面的示例针对 LoginVSI 知识工作者类型的工作负荷，通过性能测试进行验证：

* 2000并行桌面部署

* 每个桌面2vCPU。

* 每个桌面 4 GB vRAM。

* 每个桌面 50 GB 的存储

在此示例中，主机总数为18，产生每主机 VM 的密度111。

>[!IMPORTANT]
>客户工作负荷将不同于 LoginVSI 知识工作者的此示例。 在规划部署的过程中，请使用 VMware EUC SEs 满足具体的规模和性能需求。 在定版主机大小并进行相应调整之前，请务必使用实际的计划工作负荷运行自己的性能测试。

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Azure VMware 解决方案许可范围 

在 Azure VMware 解决方案上运行范围的总体成本有四个组件。 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware 解决方案容量成本

有关定价的信息，请参阅 [Azure VMware 解决方案定价](https://azure.microsoft.com/pricing/details/azure-vmware/) 页

### <a name="horizon-licensing-cost"></a>地平线许可成本

可将两个许可证用于 Azure VMware 解决方案，该解决方案可以是并发用户 (CCU) 或命名用户 (NU) ：

* 地平线订阅许可证

* 地平线通用订阅许可证

如果只是在 Azure VMware 解决方案上部署地平线来实现可预见的未来，请使用地平线订阅许可证，因为它的成本更低。

如果在 Azure VMware 解决方案和本地部署地平线，就像灾难恢复用例一样，然后选择 "通用订阅"。 通用许可证的成本较高，因为它包含用于本地部署的 vSphere 许可证。

与 VMware EUC 销售团队合作，根据您的需求来确定所需的许可成本。

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Azure 虚拟网络上的基础结构 Vm 的成本

基于标准部署体系结构，地平线基础结构 Vm 由连接服务器、UAGs、应用程序卷管理器组成，并部署在客户的 Azure 虚拟网络中。 需要额外的 Azure 本机实例以支持 Azure 上的高可用性 (HA) 、Microsoft SQL 或 Microsoft Active Directory (AD) 服务。 下面是基于2000桌面部署示例的 Azure 实例列表。 

| 地平线基础结构组件 | Azure 实例 | 2000 (所需的实例数)     | 评论  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| 连接服务器                | D4sv3          | 2       | *为 HA 包含1个实例*             |    
| UAG                              | F2sv2          | 2       | *为 HA 包含1个实例*             |
| 应用程序卷管理器              | D4sv3          | 2       | *为 HA 包含1个实例*             |
| 云连接器                  | D4sv3          | 1       |                                          |
| AD 控制器                    | D4sv3          | 2       | *用于在 Azure 上使用 MSFT AD 服务的选项* |
| MS-SQL 数据库                  | D4sv3          | 2       | *使用 Azure 上的 SQL 服务的选项*     |
| Windows 文件共享               | D4sv3          |         | *可选*                               |

在上面的示例中，每个 \$ 用户每个用户每2000月0.36 的基础结构 VM 成本量为。 请注意，此示例使用美国东部 Azure 实例定价，截至2020年6月。 根据区域、选择的选项和时间安排，定价可能会有所不同。
