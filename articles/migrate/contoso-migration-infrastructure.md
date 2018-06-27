---
title: Contoso - 设置迁移基础结构 | Microsoft Docs
description: 了解 Contoso 如何为迁移到 Azure 设置 Azure 基础结构。
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: bf861dc6317a8cc3a3ed862dfd6c133a1dcbe685
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231906"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - 部署迁移基础结构

本文介绍了 Contoso 如何设置本地和 Azure 基础结构，以便做好迁移到 Azure 的准备，并在混合环境中运行业务。

- 本文中的示例体系结构特定于 Contoso。
- 是否需要本文所述的所有元素取决于迁移策略。 例如，如果仅在 Azure 中生成云本机应用，则所需网络结构的复杂程度较低。

我们提供一系列的文章展示虚拟公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云中，而本文档是该系列的第 2 篇。 该系列介绍了背景信息，同时提供一系列部署场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移和运行不同类型的迁移。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。

**文章** | **详细信息** | **Status**
--- | --- | ---
[第 1 篇：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
文章 2：部署 Azure 基础结构（本文） | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有的 Contoso 迁移方案共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md) | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 公司使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [Azure 数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：重新托管到 Azure VM 和 SQL 托管实例](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure。 公司使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，同时使用 [Azure 数据库迁移](https://docs.microsoft.com/azure/dms/dms-overview)服务将应用数据库迁移到 SQL 托管实例上。 | 可用
[文章 5：重新托管到 Azure VM](contoso-migration-rehost-vm.md) | 展示 Contoso 如何在仅使用 Site Recovery 的情况下迁移其 SmartHotel 应用 VM。
[文章 6：重新托管到 Azure VM 和 SQL Server 可用性组](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 公司使用 Site Recovery 迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到 SQL Server 可用性组。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 演示 Contoso 如何将 Linux osTicket 应用迁移到 Azure VM。 | 可用
[文章 8：将 Linux 应用重新托管到 Azure VM 和 Azure MySQL 服务器](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 迁移 Linux osTicket 应用，以及如何使用 MySQL 工作台将其迁移（备份并还原）到 Azure MySQL 服务器实例。 | 可用

在本文中，Contoso 设置完成迁移方案所需的所有基础结构元素。 


## <a name="overview"></a>概述

需要先准备基础结构才可迁移到 Azure。  通常需要考虑五个主要方面：

**步骤 1：Azure 订阅**：如何购买 Azure，以及如何与 Azure 平台和服务交互？  
**步骤 2：混合标识**：如何在迁移后管理和控制对本地和 Azure 资源的访问权限？ 公司如何将标识管理扩展或迁移到云？  
**步骤 3：灾难恢复和复原能力**：如何确保在出现中断和灾难后复原应用和基础结构？  
**步骤 4：网络**：应如何设计网络基础结构，建立本地数据中心与 Azure 之间的连接？  
**步骤 5：安全性**：如何保护其混合/Azure 部署？  
**步骤 6：监管**：如何使其部署保持符合安全性和监管要求？

## <a name="before-you-start"></a>开始之前

在开始了解基础结构之前，可能需要先阅读有关我们在本文中讨论的 Azure 功能的一些背景信息：

- 可通过多种方式购买 Azure 的使用权限，包括即用即付、企业协议 (EA) 或 Microsoft 经销商提供的开放式许可，也可通过 Microsoft 合作伙伴（称为云解决方案提供商 (CSP)）进行购买。 了解[购买选项](https://azure.microsoft.com/pricing/purchase-options/)以及如何[整理 Azure 订阅](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/)。
- 大致了解 Azure [标识和访问管理](https://www.microsoft.com/en-us/trustcenter/security/identity)。 特别是要了解有关 [Azure AD 和将本地 AD 扩展到云](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)的信息。 我们提供了可下载的实用电子书，介绍有关[ 混合环境中标识和访问管理 (IAM)](https://azure.microsoft.com/resources/hybrid-cloud-identity/) 的信息。
- Azure 提供强大的网络基础结构，以及混合连接选项。 大致了解[网络和网络访问控制](https://docs.microsoft.com/azure/security/security-network-overview)。
- 大致了解 [Azure 安全性](https://docs.microsoft.com/azure/security/azure-security)，并了解如何创建[管理](https://docs.microsoft.com/azure/security/governance-in-azure)计划。


## <a name="on-premises-architecture"></a>本地体系结构

下图展示了 Contoso 当前的本地体系结构。

 ![Contoso 体系结构](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso 的一个主要数据中心位于美国东部的纽约市。
- 公司在美国还有 3 家当地分支机构。
- 主要数据中心通过城域光纤以太网连接到 Internet（速度 500 mbps）。
- 每家分支机构均在本地使用业务级连接实现联网，使用 IPSec VPN 隧道连接回主要数据中心。 这使得公司整个网络永久连接不掉线，同时优化了 Internet 的连接性。
- 主要数据中心通过 VMware 实现了完全的虚拟化处理。 公司有两台 ESXi 6.5 虚拟化主机，由 vCenter Server 6.5 进行托管。
- Contoso 使用 Active Directory 管理身份信息，并在内网上使用 DNS 服务器。
- 数据中心的域控制器在 VMware VM 上运行。 而当地分支机构的域控制器在物理服务器上运行。


## <a name="step-1-buy-and-subscribe-to-azure"></a>步骤 1：购买和订阅 Azure

Contoso 需要确定如何购买 Azure、如何构建订阅，以及如何对服务和资源授权。

### <a name="buy-azure"></a>购买 Azure

Contoso 通过[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 购买 Azure。 这需要对 Azure 做出前期货币承诺，同时可让公司获得众多权益，包括灵活的计费选项和最优价格。

- Contoso 预估其年度 Azure 费用。 签署协议后，全额支付第一年的费用。
- 在该年结束前，Contoso 需要使用其所有承诺使用量，否则其支付的费用会贬值。
- 如果出于某种原因超出了承诺使用量并花费了更多费用，Microsoft 将就超出的部分为公司开具发票。
- 因超出承诺使用量而产生的任何费用按照合同中的相同费率计费。 不会因超出部分而产生任何罚款。

### <a name="manage-subscriptions"></a>管理订阅

购买 Azure 后，Contoso 需要确定如何管理其订阅。 公司已签订 EA，因此对于可设置的 Azure 订阅数没有限制。

- Azure 企业许可登记表定义了 Azure 服务在公司内的形式与用法，以及核心管理结构。
- 首先，Contoso 确定一个结构（称为企业许可登记表的企业基架）。 公司使用[本文](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance)帮助了解和设计基架。
- 现在，Contoso 已决定使用函数式方法来管理其订阅。
    - 在企业内部，由一个 IT 部门来控制 Azure 预算。 该部门是具有订阅的唯一组。
    - 公司将来可扩展此模型，以便其他公司组可以作为部门加入企业许可登记表。
    - 在 IT 部门内，Contoso 安排了两个订阅：生产和开发。
    - 如果 Contoso 将来需要更多订阅，则需要管理这些订阅的访问权限、策略和符合性。 为此，可引入 [Azure 管理组](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview)，作为订阅之上的附加层。

    ![企业结构](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>检查许可

使用配置的订阅，Contoso 可以查看其 Microsoft 许可。 许可策略取决于要迁移到 Azure 中的资源，以及 Azure VM 和服务的选择和部署方式。 

#### <a name="azure-hybrid-benefit"></a>Azure 混合权益

在 Azure 中部署 VM 时，标准映像包含的许可证按使用软件的分钟数向 Contoso 收费。 但是，Contoso 是 Microsoft 长期客户，维持 EA，并具有带软件保障 (SA) 的开放式许可证。 

Azure 混合权益为 Contoso 提供了一种经济高效的迁移方法，即通过转换或重复使用软件保障涵盖的 Windows Server Datacenter 和 Standard 版本，让 Contoso 可在 Azure VM 和 SQL Server 工作负荷上保存数据。 这可让 Contoso 按照较低的 VM 和 SQL Server 基础计算费率付费。 [了解详细信息](https://azure.microsoft.com/pricing/hybrid-benefit/)。


#### <a name="license-mobility"></a>许可移动性

利用通过 SA 实现的许可移动性，Microsoft 批量许可客户（例如 Contoso）可以在 Azure 上使用有效的 SA 灵活部署合格的服务器应用。 这样就无需购买新许可证。 现有许可证没有任何关联的移动性费用，可轻松地在 Azure 中部署。 [了解详细信息](https://azure.microsoft.com/pricing/license-mobility/)。

#### <a name="reserve-instances-for-predictable-workloads"></a>保留可预测工作负荷实例

可预测工作负荷是指在 VM 运行时始终可用的工作负荷。 例如，SAP ERP 系统等业务线应用。  另一方面，不可预测的工作负荷是可变的工作负荷。 例如，在高需求期间开启、在非峰值时间关闭的 VM。

![预留实例](./media/contoso-migration-infrastructure/reserved-instance.png) 

作为对需要长时间维护的特定 VM 实例使用预留实例的交换，Contoso 可以获取折扣和优先级容量。 结合使用 [Azure 预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)和 Azure 混合权益，与常规即用即付定价相比，Contoso 可以实现 82% 的节省（2018 年 4 月）。


## <a name="step-2-manage-hybrid-identity"></a>步骤 2：管理混合标识

在整合 Azure 基础结构的过程中，一个重要的步骤是通过标识和访问管理 (IAM) 为用户提供对 Azure 资源的访问权限，并控制该访问权限。  

- Contoso 决定将其本地 Active Directory 扩展到云中，而不是在 Azure 中生成单独的新系统。
- 为此，Contoso 创建了基于 Azure 的 Active Directory。
- Contoso 没有使用 Office 365，因此需要预配新的 Azure AD。
- Office 365 使用 Azure AD 来管理用户。 如果 Contoso 在使用 Office 365，则其已有 Azure AD 租户，并使用该租户作为其主 AD。
- [深入了解](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9)用于 Office 365 的 Azure AD，并了解[如何添加订阅](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory)到现有 Azure AD。

### <a name="create-an-azure-ad"></a>创建 Azure AD

Contoso 使用 Azure 订阅中包含的 Azure AD Free 版本。 按以下步骤添加新的 AD 目录：

1. 在 [Azure 门户](http://portal.azure.com/)中，Contoso 转到“创建资源” > “标识” > “Azure Active Directory”。
2. 在“创建目录”中，指定目录名称、初始域名，以及应在其中创建 Azure AD 目录的区域。

    ![创建 Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > 创建目录时，该目录具有 domainname.onmicrosoft.com 形式的初始域名。 无法更改或删除该域名。 相反，需要将注册域名添加到 Azure AD。

### <a name="add-the-domain-name"></a>添加域名

要使用标准域名，Contoso 需要将其作为自定义名称添加到 Azure AD。 管理员可通过此选项分配熟悉的用户名。 例如，用户可以使用电子邮件地址 billg@contoso.com 登录，而无需 billg@contosomigration.onmicrosoft.com。 

为了设置自定义名称，将该名称添加到目录、添加一个 DNS 条目，然后在 Azure AD 中进行验证。

1. 在“自定义域名” > “添加自定义域”中添加域。
2. 要在 Azure 中使用 DNS 条目，需要通过域注册机构进行注册。 

    - 在“自定义域名”列表中，记下该名称的 DNS 信息。 Contoso 在使用 MX 条目。
    - 为此，需要访问名称服务器。 对于 Contoso，登录到 Contoso.com 域，并使用记录的详细信息为 Azure AD 提供的 DNS 条目创建新 MX 记录。  
1. DNS 记录传播后，在域的详细名称中单击“验证”，检查该自定义名称。

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>设置本地和 Azure 组和用户

现在，Azure AD 已启动并运行，Contoso 需要向本地 AD 组添加员工，这些员工将同步到 Azure AD。 建议使用与 Azure 中的资源组名称匹配的本地组名。 这样可以更轻松地出于同步目的标识匹配项。

#### <a name="create-resource-groups-in-azure"></a>在 Azure 中创建资源组

Azure 资源组将 Azure 资源聚集在一起。 通过使用资源组 ID，Azure 可以对组中的资源执行操作。

- Azure 订阅可以有多个资源组，但一个资源组只能存在于一个订阅中。
- 此外，一个资源组可以包含多个资源，但一个资源只能属于一个组。

Contoso 设置的 Azure 资源组如下表所示。

**资源组** | **详细信息**
--- | ---
**ContosoCobRG** | 此组包含与业务连续性 (COB) 相关的所有资源。  它包括 Contoso 在使用 Azure Site Recovery 服务和 Azure 备份服务时创建的保管库。<br/><br/> 它还包含用于迁移（包括 Azure Migrate 和数据库迁移服务）的资源。
**ContosoDevRG** | 此组包含开发和测试资源。
**ContosoFailoverRG** | 此组用作故障转移资源的登陆区域。
**ContosoNetworkingRG** | 此组包含所有网络资源。
**ContosoRG** | 此组包含与生产应用和数据库相关的资源。

Contoso 创建资源组的步骤如下：

1. 在 Azure 门户 >“资源组”中添加一个组。
2. 为每个组指定名称、组所属的订阅以及所在区域。
3. 资源组显示在“资源组”列表中。

    ![资源组](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>创建匹配的本地安全组

1. Contoso 在其本地 Active Directory 中设置了安全组，这些安全组的名称与 Azure 资源组的名称匹配。
 
    ![本地 AD 安全组](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. 为了便于管理，Contoso 创建了将添加到所有其他组的附加组。 此组具有针对 Azure 中所有资源组的权限。 可向此组添加有限数量的全局管理员。

### <a name="synchronize-ad"></a>同步 AD

Contoso 需要提供通用标识，以便访问本地和云端资源。 为此，Contoso 将本地 Active Directory 与 Azure AD 集成。 使用此模型：

- 用户和组织可以利用单个标识来访问本地应用程序和云服务（例如 Office 365），或 Internet 上成千上万的其他站点。
- 管理员可以利用 AD 中的组实现 Azure 中[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

为了便于集成，Contoso 使用 [Azure AD Connect 工具](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。 在域控制器上安装和配置工具时，该工具可将本地 AD 标识同步到 Azure AD。 

### <a name="download-the-tool"></a>下载该工具

1. 在 Azure 门户中，转到“Azure Active Directory” > “Azure AD Connect”，并将该工具的最新版本下载到用于同步的服务器。

    ![下载 AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. 通过“使用快速设置”启动“AzureADConnect.msi”安装。 这是最常用的安装方式，可用于配合使用单林拓扑和密码哈希同步进行身份验证。

    ![AD Connect 向导](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. 在“连接到 Azure AD”中，指定连接到 Azure AD 所需的凭据（以 CONTOSO\admin 或 contoso.com\admin 的形式）。

    ![AD Connect 向导](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. 在“连接到 AD DS”中，为本地 AD 指定凭据。

     ![AD Connect 向导](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. 在“已准备好配置”中，单击“配置完成后开始同步过程”立即开始同步。 然后安装。


- Contoso 与 Azure直接连接。 如果本地 AD 在代理后面，请阅读此[文章](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity)。
- 第一次同步后，可以在 Azure AD 中看到本地 AD 对象。

    ![Azure 中的本地 AD](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Contoso IT 团队根据其角色出现在每个组中。

    ![Azure 中的本地 AD 成员](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>设置 RBAC

Azure [基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 可用于对 Azure 进行细致的访问管理。 使用 RBAC，可以仅授予用户执行其作业所需的访问次数。 可将相应的 RBAC 角色分配给特定范围内的用户、组和应用程序。 角色分配的范围可以是订阅、资源组或单个资源。 

现在，Contoso 将从本地同步的角色分配到 AD 组。

1. 在 ControlCobRG 资源组中，单击“访问控制 (IAM)” > “添加”。
2. 在“添加权限” > “角色”中，选择“参与者”，然后从列表中选择“ContosoCobRG”AD 组。 该组随即出现在“所选成员”列表中。 
3. 通过将“参与者”权限添加到与资源组匹配的 AD 帐户，使用相同的权限对其他资源组重复此过程（“ContosoAzureAdmins”除外）。
4. 对于“ContosoAzureAdmins”AD 组，分配“所有者”角色。

    ![Azure 中的本地 AD 成员](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>步骤 3：针对恢复能力以及灾难进行设计

Azure 资源部署在不同区域内。
- 区域组织为地域，数据驻留、主权、符合性和恢复能力的要求在地域边界内得到遵从。
- 一个区域由一组数据中心构成。 这些数据中心部署在定义了延迟的外围中，并通过专用的区域性低延迟网络互相连接。
- 每个 Azure 区域都与另一个区域配对以获得恢复能力。
- 了解 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)以及[如何配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。


Contoso 已决定将美国东部 2（位于弗吉尼亚州）作为其主要区域，将美国中部作为其次要区域。 这样做有几点原因：

- Contoso 数据中心位于纽约，这样做考虑了到最近的数据中心的延迟。
- 美国东部 2 区域具有需要使用的所有服务和产品。 并非所有 Azure 区域都提供相同的产品和服务。 可查看 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。
- 美国中部是美国东部 2 的 Azure 配对区域。

对于混合环境，Contoso 需要考虑如何在其区域设计中融入恢复能力和灾难恢复策略。 广泛地说，策略涵盖从单区域部署（依赖容错域和区域配对等 Azure 平台功能获得恢复能力）到完整 Active-Active 模型（在其中部署了云服务和数据库，并为两个区域的用户提供服务）的所有范围。

Contoso 已决定采取折中的方式。 他们将应用和资源部署在主要区域中，而将完整基础结构保留在次要区域中，以便在应用完全出现灾难或区域故障时用作完整备份。


## <a name="step-4-design-a-network-infrastructure"></a>步骤 4：计划网络基础结构

完成区域设计后，Contoso 已准备好考虑网络策略。 他们需要考虑其本地数据中心和 Azure 如何相互连接和通信，以及如何在 Azure 中设计网络基础结构。 具体而言，需要：

**规划混合网络连接**：了解如何跨本地和 Azure 连接网络。
**设计 Azure 网络基础结构**：决定如何基于其区域部署网络。 同一区域内以及跨区域的网络如何通信。
**设计和设置 Azure 网络**：设置 Azure 网络和子网，并决定其中驻留的内容。

### <a name="plan-hybrid-network-connectivity"></a>规划混合网络连接

Contoso 为 Azure 与本地数据中心之间的混合网络规划了大量[体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)。 [详细了解](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations)比较选项。

提醒一下，Contoso 本地网络基础结构当前包含纽约的数据中心和美国东部的本地分支。  所有位置都有与 Internet 的业务级连接。  然后，每个分支可通过基于 Internet 的 IPSec VPN 隧道连接到数据中心。

![Contoso 网络](./media/contoso-migration-infrastructure/contoso-networking.png) 

Contoso 决定实现混合连接的方式如下：

1. 设置纽约 Contoso 数据中心与美国东部 2 和美国中部的两个 Azure 区域之间的新站点到站点 VPN 连接。
2. 发往 Azure 虚拟网络绑定的分支机构流量通过 Contoso 主要数据中心路由。 
3. 纵向扩展 Azure 部署时，将建立数据中心与 Azure 区域之间的 ExpressRoute 连接。 在此情况下，仅出于故障转移目的保留 VPN 站点到站点连接。
    - [深入了解](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations)如何在 VPN 与 ExpressRoute 混合连接方案之间做出选择。
    - 验证 [ExpressRoute 位置和支持](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers)。


**仅 VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN 和 ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>设计 Azure 网络基础结构

Contoso 必须以确保混合部署安全且可缩放的方式设立网络。 为此，Contoso 采取长期方法，并设计了具有恢复能力且可供企业使用的虚拟网络 (VNet)。 [深入了解](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)如何规划 VNet。

要连接两个区域，Contoso 决定实施中心到中心网络模型：

- 在每个区域中，Contoso 使用中心辐射型模型。
- 为了连接网络和中心，Contoso 使用 Azure 网络对等互连。

#### <a name="network-peering"></a>网络对等互连

Azure 提供网络对等互连以连接 VNet 和中心。 使用全球对等互连，可连接不同区域中的 VNet/中心。 本地对等互连可连接同一区域中的 VNet。 VNet 对等互连具有很多优点：

- 对等 VNet 之间的网络流量是专用的。
- VNet 之间的流量仅限于 Microsoft 主干网络。 在 VNet 之间通信不需公共 Internet、网关或加密。
- 对等互连提供不同 VNet 中资源之间默认的低延迟高带宽连接。

[深入了解](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)网络映射。

#### <a name="hub-to-hub-across-regions"></a>跨区域的中心到中心

Contoso 将在每个区域部署中心。 中心是 Azure 中的一个虚拟网络 (VNet)，充当到本地网络的连接的中心点。 中心 VNet 使用全球 VNet 对等互连彼此连接。 全球 VNet 对等互连可跨 Azure 区域连接 VNet。

- 每个区域的中心与其他区域的合作伙伴中心建立对等互连。
- 中心与其所在区域中的每个网络建立对等互连，并且可以连接到所有网络资源。

    ![全球对等互连](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>一个区域内的中心辐射

在每个区域中，Contoso 针对不同用途，以区域中心辐射网络的形式部署 VNet。 区域内的 VNet 使用对等互连连接到中心以及彼此连接。

#### <a name="design-the-hub-network"></a>设计中心网络

在 Contoso 选择的中心辐射型模型中，需要考虑如何路由本地数据中心和 Internet 发出的流量。 下面介绍了 Contoso 决定如何处理美国东部 2 和美国中部中心的路由：

- 他们设计了称为“反向 c”的网络，因为这是数据包从入站到出站网络的途径。
- 其网络体系结构有两个边界、一个不受信任的前端外围区域和一个受信任的后端区域。
- 防火墙在每个区域中都有一个网络适配器，用于控制对受信任区域的访问。
- 来自 Internet：
    - Internet 流量将到达外围网络上负载均衡的公共 IP 地址。
    - 此流量通过防火墙路由，受防火墙规则约束。
    - 实现网络访问控制后，流量转发到受信任区域中的相应位置。
    - 来自 VNet 的出站流量使用用户定义路由 (UDR) 路由到 Internet。 流量强制通过防火墙，并根据 Contoso 策略接受检查。
- 来自 Contoso 数据中心：
    - 通过 VPN 站点到站点（或 ExpressRoute）的传入流量到达 Azure VPN 网关的公共 IP 地址。
    - 流量通过防火墙路由，受防火墙规则约束。
    - 应用规则后，流量转发到受信任内部区域子网上的一个内部负载均衡器（标准 SKU）。
    - 对于通过 VPN 从受信任子网到本地数据中心的出站流量，先通过防火墙进行路由，并应用规则，再通过 VPN 站点到站点连接。



### <a name="design-and-set-up-azure-networks"></a>设计和设置 Azure 网络

设计网络和路由拓扑后，Contoso 已准备好设置其 Azure 网络和子网。

- Contoso 在 Azure 中实现 A 类专用网络（0.0.0.0 到 127.255.255.255）。 此操作可以奏效，因为 Contoso 当前在本地具有 B 类专用地址空间 172.160.0/16，可以确保地址范围之间不会出现任何重叠。
- Contoso 将在主要区域和次要区域部署 VNet。
- 他们使用包含前缀“VNET”和区域缩写“EUS2”或“CUS”的命名约定。 使用此标准，中心网络命名为“VNET-HUB-EUS2”（美国东部 2）和“VNET-HUB-CUS”（美国中部）。
- Contoso 没有 [IPAM 解决方案](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top)，因此需要规划无需 NAT 的网络路由。


#### <a name="virtual-networks-in-east-us-2"></a>美国东部 2 的虚拟网络

美国东部 2 是 Contoso 用于部署资源和服务的主要区域。 在其中构建网络的方式如下：

- **中心**：美国东部 2 的中心 VNet 是本地数据中心的主要连接中心点。
- **VNet**：美国东部 2 的辐射 VNet 可用于隔离工作负荷（如果需要）。 除了中心 VNet 外，Contoso 在美国东部 2 还有两个辐射 VNet：
    - **VNET-DEV-EUS2**。 此 VNet 为开发和测试团队提供功能完全的网络，供其开发项目使用。 它可充当生产试点区域，依赖于生产基础结构才可支持运行。
    - **VNET-PROD-EUS2**：Azure IaaS 生产组件位于此网络中。 
    -  每个 VNet 都具有自己唯一的地址空间，不会重叠。 Contoso 想要配置无需 NAT 的路由。
- **子网**：
    - 在每个网络中，每个应用层有一个对应的子网
    - 生产网络中的每个子网在开发 VNet 中具有匹配的子网。
    - 此外，生产网络中含包含域控制器子网。

下表汇总了美国东部 2 的 VNet。

**VNet** | **范围** | **对等**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2、VNET-DEV-EUS2、VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2、VNET-PROD-CUS

![主要区域的中心/辐射](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>美国东部 2 中心网络 (VNET-HUB-EUS2) 的子网

**子网/区域** | **CIDR** | **可用 IP 地址
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>美国东部 2 开发网络 (VNET-DEV-EUS2) 的子网

开发团队将开发 VNet 用作生产试点区域。 它具有三个子网。

子网 | **CIDR** | **地址** | **子网所含组件**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | 前端/Web 层 VM
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | 应用层 VM
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | 数据库 VM


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>美国东部 2 生产网络 (VNET-PROD-EUS2) 的子网

Azure IaaS 组件位于生产网络中。 每个应用层都有自己的子网。 这些子网与开发网络中的子网匹配，增添了域控制器子网。

子网 | **CIDR** | **地址** | **子网所含组件**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | 前端/Web 层 VM
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | 应用层 VM
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | 数据库 VM
**PROD-DC-EUS2** | 10.245.42.0/23 | 251 | 域控制器 VM


![中心网络体系结构](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>美国中部（次要区域）的虚拟网络

美国中部是 Contoso 的次要区域。 在其中构建网络的方式如下：

- **中心**：美国东部 2 的中心 VNet 是本地数据中心的中心连接点，美国东部 2 的辐射 VNet 可用于隔离工作负荷（如果需要），独立于其他辐射 VNet 进行管理。
- **VNet**：美国中部有两个 VNet：
    - VNET-PROD-CUS。 此 VNet 是生产网络，类似于 VNET-PROD_EUS2。 
    - VNET-ASR-CUS。 此 VNet 用作从本地故障转移后创建 VM 的位置，或进行从主要区域到次要区域的故障转移后，Azure VM 的位置。 此网络类似于生产网络，但其中没有任何域控制器。
    -  该区域中的每个 VNet 都具有自己的地址空间，不会重叠。 Contoso 想要配置无需 NAT 的路由。
- **子网**：按照与构建美国东部 2 的子网类似的方式构建子网。 区别是不需要域控制器子网。

下表汇总了美国中部的 VNet。

**VNet** | **范围** | **对等**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2、VNET-ASR-CUS、VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS、VNET-PROD-CUS
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS、VNET-ASR-CUS、VNET-PROD-EUS2  


![配对区域的中心/辐射](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>美国中部中心网络 (VNET-HUB-CUS) 的子网

子网 | **CIDR** | **可用 IP 地址**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>美国中部生产网络 (VNET-PROD-CUS) 的子网

与主要区域美国东部 2 的生产网络相同，次要区域美国中部也有一个生产网络。 

子网 | **CIDR** | **地址** | **子网所含组件**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | 前端/Web 层 VM
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | 应用层 VM
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | 数据库 VM
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | 域控制器 VM

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>美国中部故障转移/恢复网络 (VNET-ASR-CUS) 的子网

VNET-ASR-CUS 网络用于区域间的故障转移。 Site Recovery 用于复制和故障转移区域间的 Azure VM。 它还充当 Azure 网络的 Contoso 数据中心，用于仍保留在本地但故障转移到 Azure 进行灾难恢复的受保护工作负荷。

VNET-ASR-CUS 包含的基本子网与美国东部 2 的生产 VNET 中相同，但无需域控制器子网。

子网 | **CIDR** | **地址** | **子网所含组件**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | 前端/Web 层 VM
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | 应用层 VM
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | 数据库 VM

![中心网络体系结构](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>配置对等连接

每个区域的中心可与其他区域的中心以及中心区域内的所有 VNet 建立对等互连。 这样即可实现中心通信，并可查看一个区域中的所有 VNet。 请注意：

- 对等互连创建双侧连接。 一侧是第一个 VNet 上的启动对等，另一侧位于第二个 VNet 上。
- 在混合部署中，需要从本地数据中心与 Azure 之间的 VPN 连接查看对等之间传递的流量。 要启用此功能，必须为对等连接设定一些特定的设置。

对于任何通过中心从辐射 VNet 到本地数据中心的连接，Contoso 需要允许转发流量，并遍历 VPN 网关。

##### <a name="domain-controller"></a>域控制器

对于 VNET-PROD-EUS2 网络中的域控制器，Contoso 需要流量在 EUS2 中心/生产网络间流动，同时通过 VPN 连接流动到本地。 为此，需要执行以下操作：

1. 对于对等连接，选中“允许转发流量”和“允许网关传输配置”。 在我们的示例中，对等连接为 VNET-HUB-EUS2 到 VNET-PROD-EUS2 的连接。

    ![对等互连](./media/contoso-migration-infrastructure/peering1.png)

2. 对于 VNET-PROD-EUS2 到 VNET-HUB-EUS2 的连接，在对等互连的另一侧上，选中“允许转发流量”和“使用远程网关”。

    ![对等互连](./media/contoso-migration-infrastructure/peering2.png)

3. 在本地设置静态路由，通过 VPN 隧道将本地流量路由到 VNet。 在提供从 Contoso 到 Azure 的 VPN 隧道的网关上完成配置。 Contoso 使用 Windows 路由和远程访问执行此操作。

    ![对等互连](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>生产网络 

辐射对等网络无法通过中心看到另一个区域中的辐射对等网络。

两个区域中的 Contoso 生产网络要看到彼此，需要为 VNET-PROD-EUS2 和 VENT-PROD-CUS 创建直接对等连接。 

![对等互连](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>设置 DNS

在虚拟网络中部署资源时，有多种域名解析方式可供选择。 可以使用 Azure 提供的名称解析，也可提供 DNS 服务器进行解析。 使用的名称解析类型取决于资源需要以怎样的方式进行相互通信。 获取有关 Azure DNS 服务的[详细信息](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution)。

Contoso 已确定 Azure DNS 服务不适用于其混合环境。 相反，他们会利用其本地 DNS 服务器。

- 由于这是混合网络，所有本地 VM 和 Azure 中的 VM 都需要能够解析名称才能正常工作。 这意味着必须向所有 VNet 应用自定义 DNS 设置。
- Contoso 当前已在 Contoso 数据中心和分支机构部署了 DC。 其主 DNS 服务器为 CONTOSODC1(172.16.0.10) 和 CONTOSODC2(172.16.0.1)
- 部署 VNet 后，本地域控制器将设为用作网络中的 DNS 服务器。 
- 要进行此配置，在 VNet 中使用自定义 DNS 时，必须将 Azure 的递归解析程序 IP 地址（例如 168.63.129.16）添加到 DNS 列表。  为此，Contoso 在每个 VNet 中配置 DNS 服务器设置。 例如，VNET-HUB-EUS2 网络的自定义 DNS 设置如下所示：
    
    ![自定义 DNS](./media/contoso-migration-infrastructure/custom-dns.png)

除了本地域控制器，Contoso 还将实现四个域控制器以支持其 Azure 网络，并为每个区域实现两个域控制器。 下面是他们将在 Azure 中部署的内容。

**区域** | **DC** | **VNet** | 子网 | IP 地址
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
CUS | CONTOSODC5 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4
CUS | CONTOSODC6 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4

在部署本地域控制器后，Contoso 需要更新任一区域中的网络 DNS 设置，以在其 DNS 服务器列表中包括新的域控制器。



#### <a name="set-up-domain-controllers-in-azure"></a>在 Azure 中设置域控制器

在更新网络设置后，Contoso 已准备好在 Azure 中构建出域控制器。

1. 在 Azure 门户中，将新的 Windows Server VM 部署到相应的 VNet。
2. 在 VM 的每个位置创建可用性集。 可用性集可执行以下操作：
    - 确保 Azure 结构将 VM 分隔到 Azure 区域中的不同基础结构。 
    -  使 Contoso 能够为 Azure 中的 VM 获取 99.95% 的 SLA。  [了解详细信息](https://docs.microsoftcom/azure/virtual-machines/windows/regions-and-availability#availability-sets)。

    ![可用性组](./media/contoso-migration-infrastructure/availability-group.png) 
3. 部署 VM 后，打开 VM 的网络接口。 在其中将专用 IP 地址设为静态，然后指定一个有效地址。

    ![VM NIC](./media/contoso-migration-infrastructure/vm-nic.png)

4. 现在，将新的数据磁盘附加到 VM。 此磁盘包含 Active Directory 数据库和 sysvol 共享。 
    - 磁盘的大小将确定支持的 IOPS 数。
    - 随着时间的推移，磁盘大小可能需要随着环境增长而增大。
    - 不应将驱动器设置为针对主机缓存进行读/写。 Active Directory 数据库不支持此操作。

     ![Active Directory 磁盘](./media/contoso-migration-infrastructure/ad-disk.png)

5. 添加磁盘后，通过远程桌面连接到 VM，并打开服务器管理器。
6. 然后在“文件和存储服务”中运行“新建卷向导”，确保本地 VM 上的驱动器号为 F: 或之后的字母。

     ![新建卷向导](./media/contoso-migration-infrastructure/volume-wizard.png)

7. 在“服务器管理器”中添加“Active Directory 域服务”角色。 然后，将 VM 配置为域控制器。

      ![服务器角色](./media/contoso-migration-infrastructure/server-role.png)  

9. 将 VM 配置为 DC 并重启后，打开 DNS 管理器，将 Azure DNS 解析程序配置为转发器。  这样，DC 可转发它无法在 Azure DNS 中解析的 DNS 查询。

    ![DNS 转发器](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. 现在，使用 VNet 区域的相应域控制器更新每个 VNet 的自定义 DNS 设置。 在列表中纳入本地 DC。

### <a name="set-up-active-directory"></a>设置 Active Directory

AD 是一项重要网络服务，必须正确配置。 Contoso 可为 Contoso 数据中心以及 EUS2 和 CUS 区域生成 AD 站点。  

1. 他们创建两个新的站点（AZURE-EUS2 和 AZURE-CUS）以及数据中心站点 (ContosoDatacenter)。
2. 创建站点后，在站点中创建子网，以匹配 VNet 和数据中心。

    ![DC 子网](./media/contoso-migration-infrastructure/dc-subnets.png)

3. 然后，创建两个站点链接，连接所有内容。 然后，应将域控制器移动到相应的位置。

    ![DC 链接](./media/contoso-migration-infrastructure/dc-links.png)

4. 配置所有内容后，Active Directory 复制拓扑已就位。
    
    ![DC 复制](./media/contoso-migration-infrastructure/ad-resolution.png)

5. 完成所有操作后，本地 Active Directory 管理中心会显示域控制器和站点列表。

    ![AD 管理中心](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>步骤 5：规划管理

Azure 提供了一系列跨服务和 Azure 平台的管理控制。 [详细了解](https://docs.microsoft.com/azure/security/governance-in-azure)各选项的基本信息。

在配置标识和访问控制时，Contoso 已经开始实施管理和安全性的某些方面。 广泛地说，需要考虑三个方面：

- **策略**：Azure 中的策略对资源应用并强制实施规则和效果，以便资源始终符合公司要求和 SLA。
- **锁定**：Azure 允许锁定订阅、资源组和其他资源，以便只有获得授权的用户才可进行修改。
- **标记**：看通过标记控制、审核和管理资源。 标记可将元数据附加到资源，提供有关资源或所有者的信息。

### <a name="set-up-policies"></a>设置策略

Azure 策略服务对资源进行评估，找到与所定义策略不相符的资源。 例如，可能有策略只允许某种类型的 VM，或要求资源具有特定标记。 

Azure 策略指定策略定义，而策略分配指定策略的应用范围。 该范围可以是从管理组到资源组。 [了解](https://docs.microsoft.com/azure/azure-policy/create-manage-policy)如何创建和管理策略。

Contoso 想要开始使用几个策略：

- 他们想要一个确保只能在 EUS2 和 CUS 区域中部署资源的策略。
- 他们想要将 VM SKU 仅限于已批准的 SKU。 目的是确保不使用昂贵的 VM SKU。

#### <a name="limit-resources-to-regions"></a>限制资源区域

Contoso 使用内置策略定义“允许的位置”来限制资源区域。

1. 在 Azure 门户中，单击“所有服务”，搜索“策略”。
2. 选择“分配” > “分配策略”。
3. 在策略列表中，选择“允许的位置”。
4. 将“范围”设置为 Azure 订阅的名称，并选择允许列表中的两个区域。

    ![策略允许的区域](./media/contoso-migration-infrastructure/policy-region.png)

5. 默认情况下，策略设置为“拒绝”，这意味着，如果有人使用订阅在 EUS2 或 CUS 之外的区域开始部署，部署将失败。 下面列出了某人使用 Contoso 订阅尝试在美国西部设置部署时会发生的情况。

    ![策略失败](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>允许特定 VM SKU

Contoso 使用内置策略定义“允许的虚拟机 SKU”来限制可以在订阅中创建的 VM 类型。 

![策略 SKU](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>检查策略符合性

策略立即生效，Contoso 可以检查资源的符合性。 

1. 在 Azure 门户中，单击“符合性”链接。
2. 符合性仪表板随即显示。 可以向下钻取更多详细信息。

    ![策略符合性](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>设置锁定

Contoso 长期使用 ITIL 框架管理其系统。 该框架最重要的一个方面变更控制，而 Contoso 希望确保在其 Azure 部署中实现该变更控制。

Contoso 实现锁定的方式如下：

- 任何生产或故障转移组件都必须包含在具有 ReadOnly 锁定的资源组中。  这意味着，若要修改或删除生产项目，必须先删除锁定。 
- 非生产资源组具有 CanNotDelete 锁定。 这意味着经过授权的用户可以读取或修改某个资源，但无法删除。

[深入了解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)锁定。

### <a name="set-up-tagging"></a>设置标记

为了跟踪添加的资源，将资源与相应的部门、客户和环境关联对 Contoso 变得越发重要。 

除了提供有关资源和所有者的信息外，标记还可让 Contoso 聚合资源和对资源分组，以及出于退款目的使用该数据。

Contoso 需要可视化其 Azure 资产，使其适用于业务。 例如，角色或部门。 请注意，资源不需要驻留在同一个资源组中就可共享一个标记。 为此，Contoso 创建一个简单的标记分类，以便每个人都使用相同的标记。

**标记名称** | **值**
--- | ---
CostCenter | 12345：它必须是 SAP 中的有效成本中心。
BusinessUnit | 业务部门的名称（SAP 中）。 与 CostCenter 匹配。
ApplicationTeam | 可支持应用的团队的电子邮件别名。
CatalogName | 应用或 ShareServices 的名称（根据资源组支持的服务目录）。
ServiceManager | 资源的 ITIL 服务管理者的电子邮件别名。
COBPriority | 企业为 BCDR 设置的优先级。 值为 1-5。
ENV | DEV、STG 和 PROD 是可能的值。 分别表示开发、暂存和生产。

例如： 

 ![Azure 标记](./media/contoso-migration-infrastructure/azure-tag.png)

创建标记后，Contoso 将回过头来创建新的 Azure 策略定义和分配，强制在整个组织内使用所需标记。


## <a name="step-6-consider-security"></a>步骤 6：考虑安全性

在云中，安全性非常重要，Azure 提供了多种不同的安全工具和功能。 这些工具和功能可帮助在安全的 Azure 平台上创建安全的解决方案。 阅读[可信云中的置信度](https://azure.microsoft.com/overview/trusted-cloud/)了解有关 Azure 安全性的详细信息。

Contoso 需要考虑几个主要方面

- **Azure 安全中心**：Azure 安全中心跨混合云工作负荷提供统一的安全管理和高级威胁防护。 有了安全中心，即可对各种工作负荷应用安全策略、减少受到的威胁，以及检测和响应攻击。  [了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-intro)。
- **网络安全组 (NSG)**：NSG 是一个筛选器（防火墙），包含一系列安全规则，通过应用这些规则，可以允许或拒绝流向连接到 Azure VNet 的资源的网络流量。 [了解详细信息](https://docs.microsoft.com/azure/virtual-network/security-overview)。
- **数据加密**：Azure 磁盘加密是用于帮助加密 Windows 和 Linux IaaS 虚拟机磁盘的功能。 [了解详细信息](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)。

### <a name="work-with-the-azure-security-center"></a>使用 Azure 安全中心

Contoso 想要快速查看新的混合云（尤其是 Azure 工作负荷）的安全态势。  因此，Contoso 决定实现 Azure 安全中心，并开始使用以下功能： 

- 集中式策略管理
- 持续评估
- 可行的建议 

#### <a name="centralize-policy-management"></a>集中管理策略

使用集中式策略管理，Contoso 可通过集中管理整个环境中的安全策略，确保符合安全要求。 可以快速轻松地实现适用于其所有 Azure 资源的策略。

![安全策略](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>评估和操作

Contoso 可利用持续安全评估监视计算机、网络、存储、数据和应用程序的安全，发现潜在的安全问题。 

- 安全中心可分析 Contoso 的计算、基础架构和数据资源的安全状态，以及 Azure 应用和服务的安全状态。
- 持续评估可帮助 Contoso 运营团队发现潜在的安全问题，例如系统缺少安全更新，或者网络端口被公开。 
- Contoso 希望确保其所有 VM 得到保护。 安全中心可帮助达成此目标，通过验证 VM 运行状况、确定优先级和提供可行的建议，在攻击者利用安全漏洞前修复这些漏洞。

![监视](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>使用 NSG

Contoso 可以使用网络安全组限制发往虚拟网络中的资源的网络流量。

- 网络安全组包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。
- 向某个子网应用规则时，该规则会应用到该子网中的所有资源。 除了网络接口外，这还包括子网中部署的 Azure 服务实例。
- 使用应用程序安全组 (ASG)，可将网络安全性配置为应用结构的固有扩展，从而可以基于这些组将 VM 分组以及定义网络安全策略。
    - 使用应用安全组，可以大量重复使用安全策略，而无需手动维护显式 IP 地址。 平台会处理显式 IP 地址和多个规则集存在的复杂性，让你专注于业务逻辑。
    - 可将应用程序安全组指定为安全规则中的源和目标。 定义安全策略后，可以创建 VM，并将 VM NIC 分配到组。 


Contoso 将混合实现 NSG 和 ASG。 他们关注的是 NSG 管理， 担心过度使用 NSG 会为操作人员增加麻烦。  考虑到这一点，他们采用两个关键主体，使用一个一般规则：

- 流入和留出所有子网的所有流量（北-南）都遵循 NSG 规则，中心网络的 GatewaySubnets 除外。
- 任何防火墙或域控制器都同时受到子网 NSG 和 NIC NSG 的保护。
- 所有生成应用程序都应用了 ASG。


Contoso 已针对其应用程序生成了相关模型。

![“安全”](./media/contoso-migration-infrastructure/asg.png)


使用最低特权配置与 ASG 关联的 NSG，以确保只有经过允许的数据包可以从网络的一部分流到其目标。

**Action** | 名称 | **源** | **目标** | 端口
--- | --- | --- | --- | --- 
允许 | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80、443
允许 | AllowWebToApp | APP1-FE | APP1-DB | 1433
允许 | AllowAppToDB | APP1-APP | 任意 | 任意
拒绝 | DenyAllInbound | 任意 | 任意 | 任意

### <a name="encrypt-data"></a>加密数据

Azure 磁盘加密与 Azure Key Vault 集成，帮助控制和管理 Key Vault 订阅中的磁盘加密密钥和机密。 它可确保 VM 磁盘上的所有数据在 Azure 存储中静态加密。  

- Contoso 已确定特定 VM 需要加密。
- 他们将对包含客户数据、机密或 PPI 数据的 VM 应用加密。


## <a name="conclusion"></a>结束语

在本文中，Contoso 设置了其 Azure 基础结构，并为 Azure 订阅、混合标识、灾难恢复、网络、管理和安全性设置或规划了基础结构策略。 

对于本文中 Contoso 完成的步骤，并非需要执行所有这些步骤才可迁移到云。 Contoso 想要规划可用于所有类型的迁移，并且安全、具有恢复能力且可缩放的网络基础结构。 

设立此基础结构后，他们就已准备就绪，可尝试迁移。

## <a name="next-steps"></a>后续步骤

作为第一个迁移方案，Contoso 将[评估其要迁移到 Azure 的本地 SmartHotel 双层应用](contoso-migration-assessment.md)。 
