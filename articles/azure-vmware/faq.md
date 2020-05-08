---
title: 常见问题
description: 提供有关 Azure VMware 解决方案（AVS）常见问题的解答。
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 6d8e87dd52871b82109ccc794af04244efe95b06
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854595"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>有关 Azure VMware 解决方案（AVS）预览版的常见问题

有关 Azure VMware 解决方案（AVS）常见问题的解答。

## <a name="general"></a>常规

**什么是 Azure VMware 解决方案 (AVS)？**

随着企业采用 IT 现代化策略来提高业务灵活性、降低成本和加速创新，混合云平台已成为客户数字转换的关键启用能力。 AVS 将 VMware 的软件定义数据中心（SDDC）软件与 Microsoft Azure 全局云服务生态系统相结合。 可以通过管理 AVS 解决方案来满足性能、可用性、安全性和符合性要求。

## <a name="avs-service"></a>AVS 服务

**现在，可以在何处使用 AVS？**

预览期间，将在美国东部的北美和西欧的阿姆斯特丹提供此功能。

**Azure VMware 解决方案（AVS）实例中运行的工作负荷是使用还是与 Azure 服务集成？**

所有 Azure 服务都将可供 AVS 解决方案客户使用。 特定服务的性能和可用性限制需要根据具体情况进行寻址。

**我是否使用现在使用的工具来管理私有云资源？**

可以。 Azure 门户用于部署和大量管理操作。 vCenter 和 NSX Manager 用于管理 vSphere 和 NSX 资源。

**能否使用本地 vCenter 管理私有云？**

在启动时，AVS 不支持跨本地和私有云环境的单一管理体验。 私有云群集将使用 vCenter 进行管理，并且本地到私有云的 NSX 管理器。

**是否可以使用本地运行的 vRealize Suite？** 

可以根据具体情况评估具体的集成和用例。

**能否将 vSphere Vm 从本地环境迁移到 AVS 私有云？**

可以。 如果满足标准跨 vCenter [vMotion 要求] [https://kb.vmware.com/s/article/210695]，则可以使用 VM 迁移和 VMotion 将 vm 移到私有云。

**本地环境中是否需要 vSphere 的特定版本？**

由于所有云环境都附带了 HCX、vSphere 5.5 或更高版本。

**更改控制过程看起来是什么样子？**

对服务本身所做的更新将遵循 Microsoft Azure 的标准更改管理流程。 客户负责任何工作负荷管理任务和相关的更改管理过程。

**这与 Azure VMware 解决方案的 CloudSimple 有何不同？**

使用新的 Azure VMware 解决方案，Microsoft 和 VMware 提供直接的云提供商合作关系。 新解决方案完全由 Microsoft 设计、构建和支持，并由 VMware 认可。 从体系结构上讲，解决方案是一致的，VMware 技术堆栈是在 Azure 专用基础结构上运行的。

**如果我是现有的 Azure VMware 解决方案客户，此预览版对我意味着什么？**

CloudSimple 不会更改现有的 Azure VMware 解决方案。 我们将继续支持 Azure 上的解决方案。 Azure VMware 解决方案由我们的服务级别协议[（SLA）](https://aka.ms/CSVMwareSLA)提供支持。 客户应继续使用服务进行生产工作负荷;这是由[Microsoft 的服务条款](https://azure.microsoft.com/support/legal/)控制的可用解决方案。

**能否通过 CloudSimple 将 Azure VMware 解决方案迁移到这个新的解决方案？**

是的，Azure VMware 解决方案支持使用熟悉的 VMware 工具（如 HCX）进行迁移。 对于对迁移到新解决方案感兴趣的客户，请与你的 Microsoft 帐户团队合作来探索选项和可用支持。

## <a name="compute-network-and-storage"></a>计算、网络和存储

**有多种类型的主机可用吗？**

只有一种类型的主机可用。

**每种类型的主机中的 CPU 规格是什么？**

服务器具有2个18核 2.3 GHz Intel Cpu。

**每个主机中有多少内存？**

服务器的 RAM 为 576 GB。

**每个主机的存储容量是多少？**

每个 ESXi 主机都有两个 VSAN diskgroups，容量层为 15.2 TB，有 3.2 TB 个 NVMe 缓存层（每个 diskgroup 中 1.6 TB）。

**每个 ESXi 主机中有多少网络带宽？**

ESXi 主机支持最大为 25 Gbps 的连接带宽。

**VSAN 数据存储上存储的数据是否静态加密？**

是的，默认情况下，使用存储在 Azure Key Vault 中的密钥加密所有 VSAN 数据。

## <a name="hosts-clusters-and-private-clouds"></a>主机、群集和私有云

**底层基础结构是否共享？**

不会，私有云主机和群集在使用之前和之后都是专用和安全删除的。

**每个群集的主机的最小和最大数目是多少？**

群集可以在3到16个 ESXi 主机之间进行扩展。 试用群集限制为三个主机。

**能否扩展私有云群集？**

是的，群集在最小和最大 ESXi 主机数之间进行缩放。 试用群集限制为三个主机。

**什么是试用群集？**

试用群集是三个主机群集，用于一个月评估的 AVS 私有云。

**是否可以为试用群集使用高端主机？**

否。 高端 ESXi 主机保留用于生产群集。

## <a name="avs-and-vmware-software"></a>AVS 和 VMware 软件

**私有云中使用的 VMware 软件版本是什么？**

私有云使用 vSphere 6.7、vSAN 6.7、HCX 和版本2.5。  

**是否使用 VMware NSX 执行私有云？**

是的，NSX-T 2.5 用于 AVS 私有云中软件定义的网络。

**能否在私有云中使用 VMware NSX？**

否。 NSX 是唯一受支持的 NSX 版本。

**本地环境或连接到私有云的网络中是否需要 NSX。**

不需要，不需要在本地使用 NSX。

**在私有云中，VMware 软件的升级和更新计划是什么？**

私有云软件捆绑升级的目的是将软件保存到最新版本的软件捆绑（VMware）版本内。 私有云软件版本可能不同于各个软件组件的最新版本（ESXi、NSX-T、vCenter、VSAN）。

**私有云软件堆栈的更新频率是多少？**

私有云软件将按计划升级，该计划将从 VMware 中的软件包发行版进行跟踪。 私有云不需要停机就能进行升级。

## <a name="connectivity"></a>连接

**将私有云与本地环境结合起来需要哪些网络 IP 地址计划？**

部署 AVS 私有云需要专用网络/22 地址空间。 此专用地址空间不应与订阅中的其他虚拟网络或本地网络重叠。
 
**如何实现从本地环境连接到 AVS 私有云？**

可以通过以下两种方法之一连接到服务： 

- 使用部署在 Azure 虚拟网络上的 VM 或应用程序网关通过 ExpressRoute 对等互连到私有云。
- 通过 ExpressRoute Global Reach 从本地数据中心到 Azure ExpressRoute 线路。

**如何实现将工作负荷 VM 连接到 internet 或 Azure 服务终结点？**

在 Azure 门户中，为私有云启用 internet 连接。 使用 "NSX-t 管理器" 创建一个 NSX-T T1 路由器和逻辑交换机。 然后，使用 vCenter 在逻辑交换机定义的网络段上部署 VM。 该 VM 将具有对 internet 和 Azure 服务的网络访问权限。

**是否需要限制从 internet 到私有云中逻辑网络上的 Vm 的访问权限？**

否。 不允许将网络流量从 internet 直接连接到私有云。

**是否需要限制从逻辑网络上的 Vm 到 internet 的 internet 访问？**

可以。 需要使用 "NSX-T 管理器" 创建防火墙，以限制 VM 对 internet 的访问。

## <a name="accounts-and-privileges"></a>帐户和特权

**我的新的 AVS 私有云将会获得哪些帐户和特权？**

你将在 cloudadmin 用户的 vCenter 和 NSX-T 管理器中提供管理员访问权限。 还有一个可用于合并 Azure Active Directory 的 CloudAdmin 组。 有关详细信息，请参阅[访问和标识概念](concepts-identity.md)。

**可以让管理员访问 ESXi 主机？**

不可以，对 ESXi 的管理员权限限制为满足解决方案的安全要求。

**你将在 vCenter 中有哪些权限？**

你将拥有 CloudAdmin 组权限。 有关详细信息，请参阅[访问和标识概念](concepts-identity.md)。

**我将对 NSX T 管理器使用哪些权限？**

你将在 NSX 上拥有完全的管理员权限，并且可以管理基于角色的访问控制，就像在本地使用 NSX-T 数据中心一样。 有关详细信息，请参阅[访问和标识概念](concepts-identity.md)。

> [!NOTE]
> 将在私有云部署过程中创建并配置 T0 路由器。 对该逻辑路由器或 NSX 边缘节点 Vm 的任何修改都可能会影响与私有云的连接。

## <a name="billing-and-support"></a>计费和支持

**在 AVS 预览过程中如何计费**

预览期间使用的 AVS 计费按即用即付。 其他选项将在正式发布时可用。

**在 AVS 预览期间，如何确定定价的定价？**

有关定价的一般问题，请参阅 Azure VMware 解决方案[定价](https://azure.microsoft.com/pricing/details/azure-vmware)页。 预览版提供按要求提供，请联系你的帐户团队，或单击定价页面上的链接以联系销售人员。

**谁支持 AVS？**

Microsoft 提供了对 AVS 的支持。 请注意，根据我们的预览准则，我们将在周一至周五上午9到 5 pm 的 PST 营业时间提供支持。 可以通过[此链接](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)提出支持票证

**需要哪些帐户才能创建 AVS 私有云？**

你将需要 azure 订阅中的一个 Azure 帐户。

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
