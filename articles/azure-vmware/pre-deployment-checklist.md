---
title: Azure VMware 解决方案预部署清单
description: 使用此清单作为预先部署规划过程的一部分。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579573"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Azure VMware 解决方案预部署清单
你将在 [规划阶段](production-ready-deployment-steps.md)使用此预部署清单。

## <a name="success-criteria"></a>成功条件
定义要运行的测试和预期结果。

| 所需的基本信息 | 你的响应 |
| ----------- | ------------- |
| 部署 SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 创建虚拟网络 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 创建跳转框 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 创建虚拟网络网关 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 连接 ExpressRoute Global Reach | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| 登录到 NSX Manager 和 vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| 需要的主要信息 | 你的响应 |
| --------| --------------|
| 创建 DHCP 服务器 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 创建网络段 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 缩放 (添加或删除节点)  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 部署 VMware HCX | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
|  (Vm 创建虚拟机)  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| 启用 internet | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 从本地到私有云的 VM 迁移 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| 所需的其他信息 | 你的响应 |
| --------| --------------|
| VM 快照操作 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| 部署 NSX-T 负载均衡器 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure 集成<br><ul><li>共享内容库</li><li>安全集成</li><li>上传 ISO</li><li>从 ISO 生成</li><li>Azure 备份</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 微细分 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 路由 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Azure VMware 解决方案信息

#### <a name="azure-subscription"></a>Azure 订阅
提供 Azure VMware 解决方案的订阅名称和订阅 ID。 订阅可以是新订阅或现有订阅。 不要使用开发/测试订阅。

| 所需信息  | 你的响应 |
| ------------| ------------- |
| 订阅和 ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 使用 EA 订阅 | ☐是 &nbsp; &nbsp; ☐否  |
| 资源组名称 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 位置 | ☒美国东部 |
| Azure 管理员<br><br>提供管理员的姓名和联系人<br>分配用于从 marketplace 启用服务。<br>参与者是所需的最小角色 <br>[注册 Azure VMware 解决方案资源提供程序](tutorial-create-private-cloud.md#register-the-resource-provider)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>本地 VMware 信息

| 所需信息  | 你的响应 |
| ------------| --------------|
| vSphere 版本 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| vCenter 版本 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vCenter 管理 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| L2 扩展<br><br>如果通过 VMware HCX 扩展 L2 网络，<br>提供要扩展的本地网络。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>提供整个环境中使用的 vSwitch 类型。 | &nbsp; &nbsp; 分布式☐☐标准<br><br>如果使用标准版，则 VMware HCX 不可用。 |
| DNS 和 DHCP<br><br>需要适当的 DNS 和 DHCP 基础结构。 <br>建议使用内置于中的 DHCP 服务 <br>NSX 或使用私有云中的本地 DHCP 服务器 <br>而不是通过路由广播 DHCP 流量 <br>WAN 回到本地。 有关详细信息，请参阅： <br>请参阅 [如何创建和管理 Azure VMWare 解决方案中的 DHCP](manage-dhcp.md)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>网络-Azure VMware 解决方案基础结构 
所需的数据是为了帮助你满足 Azure VMware 解决方案网络对于初始和初始网络测试的需求。 

| 所需信息 | 你的响应 |
| ----------- | ------------- |
|Azure VMware 解决方案 CIDR<br><br>对于 vSphere 主机、vSAN 和管理是必需的 <br>Azure VMware 解决方案中的网络。 有关 <br>有关信息，请参阅 [路由和子网注意事项](tutorial-network-checklist.md#routing-and-subnet-considerations)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure VMware 解决方案工作负荷 CIDR (可选) <br><br>分配要在 Azure VMware 中使用的网络<br>用于初始测试的解决方案。 虚拟机<br>将部署来验证网络连接 <br>from/to Azure VMware 解决方案。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>网络-将 Azure VMware 解决方案连接到 Azure 虚拟网络
考验 Azure VMware 解决方案群集后所需的数据可以通过 ExpressRoute 线路连接到 Azure，这是 Azure VMware 解决方案服务的一部分。

| 所需信息 | 你的响应 |
| ------------------ | ------------- |
| 跳转框-Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 创建虚拟网络 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 创建网关子网 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 创建虚拟网络网关<br><br>有关详细信息，请参阅 [创建虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>网络-将 Azure VMware 解决方案连接到本地数据中心

| 所需信息 | 你的响应 |
| ------------------ | ------------- |
| ExpressRoute 对等 CIDR<br><br>`/29`CIDR 地址块。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute 授权密钥和资源 ID<br><br>提供授权密钥和资源 ID， <br>它是从当前 ExpressRoute 生成的 <br>连接到本地数据中心的线路。  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 默认路由的方向<br><br>Azure VMware 解决方案中的虚拟机应该 <br>通过提供的 Azure VMware 解决方案访问 internet <br>internet，或返回到 <br>默认路由的本地？ | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 与服务通信所需的网络端口<br><br>有关详细信息，请参阅 [所需的网络端口](tutorial-network-checklist.md#required-network-ports)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>网络-VMware HCX

| 所需信息 | 你的响应 |
| ------------------ | ------------- |
| 网络端口<br><br>如果有防火墙，请确保所需的网络端口 <br>在本地与 Azure VMware 解决方案之间打开。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>有关配置 DNS 的信息， <br>请参阅 [网络-Azure VMware 解决方案基础结构](#networking---azure-vmware-solution-infrastructure)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX CIDRs<br><br>需要两个 `/29` CIDR 块，它们用于 <br>本地 VMware HCX 基础结构组件。  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

