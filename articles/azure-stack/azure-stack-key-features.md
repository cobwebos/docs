---
title: "Azure Stack 中的重要功能和概念 | Microsoft Docs"
description: "了解 Azure Stack 中的重要功能和概念。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: 
ms.openlocfilehash: b773ddc5da12f92960ef3378decac8569dac9ab9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Azure Stack 中的重要功能和概念

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

如果不太熟悉 Microsoft Azure Stack，本文的术语和功能说明可能会有所帮助。

## <a name="personas"></a>角色
有两个类型的 Microsoft Azure 堆栈，云操作员 （提供程序） 和租户 （使用者） 的用户。

* A**云操作员**可以配置 Azure 堆栈和管理产品/服务、 计划、 服务、 配额和定价若要为其租户提供资源。  云操作员还管理容量，并响应警报。  
* **租户**（也称为用户）使用云管理员提供的服务。 租户可以预配、监视和管理他们订阅的服务，例如 Web 应用、存储和虚拟机。

## <a name="portal"></a>门户
与 Microsoft Azure Stack 交互的主要方法包括管理员门户、用户门户和 PowerShell。

每个 Azure Stack 门户由 Azure 资源管理器的单独实例提供支持。  云操作员使用管理员门户来管理 Azure 堆栈，并执行某些操作，如创建产品的租户。  用户门户（也称为租户门户）提供自助服务体验让用户使用云资源，例如虚拟机、存储帐户和 Web 应用。 有关详细信息，请参阅 [Using the Azure Stack administrator and user portals](azure-stack-manage-portals.md)（使用 Azure Stack 管理员门户和用户门户）。

## <a name="identity"></a>标识 
Azure 堆栈使用 Azure Active Directory (AAD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识提供程序。  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory 是 Microsoft 的基于云的多租户标识提供者。  大多数混合方案中使用 Azure Active Directory 作为标识存储。

### <a name="active-directory-federation-services"></a>Active Directory 联合身份验证服务
对于断开连接的 Azure Stack 部署，可以选择使用 Active Directory 联合身份验证服务 (AD FS)。  Azure Stack、资源提供者和其他应用程序使用 AD FS 的方式与它们使用 Azure Active Directory 的方式非常类似。 Azure Stack 包含自身的 AD FS 和 Active Directory 实例，另外还包含 Active Directory 图形 API。 Azure 堆栈开发工具包支持以下 AD FS 方案：

- 通过使用 AD FS 登录到部署。
- 使用 Key Vault 中的机密创建虚拟机
- 创建保管库用于存储/访问机密
- 在订阅中创建自定义 RBAC 角色
- 将资源的用户分配到 RBAC 角色
- 通过 Azure PowerShell 创建系统范围的 RBAC 角色
- 以用户身份通过 Azure PowerShell 登录
- 创建服务主体并使用它们登录到 Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>区域、服务、计划、产品和订阅
在 Azure Stack 中，使用区域、订阅、服务和计划将服务传送到租户。 租户可以订阅多个产品。 产品可以包含一个或多个计划，计划可以包含一个或多个服务。

![](media/azure-stack-key-features/image4.png)

租户产品订阅的示例层次结构，每个订阅包含不同的计划和服务。

### <a name="regions"></a>区域
Azure Stack 区域是规模与管理的基本要素。 组织可以创建多个区域，并在每个区域中分配资源。 还可以在区域中包含不同的服务产品。 在 Azure 堆栈开发工具包中，单个区域受支持，然后将自动命名*本地*。

### <a name="services"></a>服务
提供者可以使用 Microsoft Azure Stack 传送多种多样的服务和应用程序，例如虚拟机、SQL Server 数据库、SharePoint、Exchange，等等。

### <a name="plans"></a>计划
计划是对一个或多个服务的分组。 提供者可以创建要提供给租户的计划。 反过来，租户可以订阅产品/服务，以便使用其所包括的计划和服务。

可以使用配额设置来配置已添加到计划的每个服务，以帮助管理云容量。 配额可以包括限制（例如 VM、RAM 和 CPU 限制），并应用到每个用户订阅。 配额可按位置区分。 例如，包含从区域 A 的计算服务的规划可能有两个虚拟机、 4 GB RAM 和 10 个 CPU 内核的配额。

服务管理员在创建时提供产品，可以包括**基本计划**。 当租户订阅该产品时，默认会包括这些基本计划。 如果用户订阅了 （和创建订阅），用户将有权访问 （具有相应的配额） 这些基本计划中指定的所有资源提供程序。

服务管理员还可以在产品中包含**附加计划**。 默认情况下，订阅中不包含附加计划。 附加计划是产品中提供的额外计划（配额），订阅所有者可将其添加到自己的订阅中。

### <a name="offers"></a>产品
产品是提供者提供给租户购买（订阅）的一个或多个计划。 例如，产品 Alpha 可能包含计划 A 和计划 B，这两个计划分别包含一组计算服务和一组存储与网络服务。

产品附带一组基本计划。服务管理员可以创建附加计划，租户可将这些附加计划添加到其订阅中。

### <a name="subscriptions"></a>订阅
租户可通过订阅购买产品。 订阅是租户与产品的组合。 租户可以购买多个产品的订阅。 每个订阅仅适用于一个产品。 租户的订阅确定了他们可以访问哪些计划/服务。

订阅可帮助组织和访问云资源和服务提供程序。

对于管理员，在部署过程时，会创建默认提供程序订阅。 此订阅可以用于管理 Azure 堆栈，进一步部署资源提供程序，并为租户创建计划，并提供。 不应该用于运行客户工作负荷和应用程序。 


## <a name="azure-resource-manager"></a>Azure 资源管理器
借助 Azure 资源管理器，可在基于模板的声明性模型中使用基础结构资源。   它提供可用于部署和管理你的解决方案组件的单一界面。 有关完整信息和指南，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。

### <a name="resource-groups"></a>资源组
资源组是资源、服务和应用程序的集合 — 每个资源都有一种类型，例如虚拟机、虚拟网络、公共 IP、存储帐户和网站。 每个资源必须在资源组中，因此，资源组有助于以逻辑方式组织资源，例如，按工作负荷或位置进行组织。  在 Microsoft Azure Stack 中，计划和产品等资源也资源组中管理。
 
### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板
使用 Azure 资源管理器中，你可以创建模板 （采用 JSON 格式），用于定义部署和配置你的应用程序。 此模板称为 Azure 资源管理器模板，并提供声明性方式定义部署。 使用模板可以在整个应用程序生命周期内反复部署该应用程序，并确保以一致的状态部署资源。

## <a name="resource-providers-rps"></a>资源提供程序 (RPs)
资源提供程序属于 Web 服务，构成了所有基于 Azure 的 IaaS 和 PaaS 服务的基础。 Azure 资源管理器依赖于不同的 RPs 以提供对服务的访问。

有四个基础 RP：网络、存储、计算和 KeyVault。 每个这些 RPs 可帮助你配置和控制其各自的资源。 服务管理员还可以添加新的自定义资源提供程序。

### <a name="compute-rp"></a>计算 RP
Azure Stack 租户可以使用计算资源提供程序 (CRP) 创建自己的虚拟机。 CRP 包括创建虚拟机和虚拟机扩展的功能。 虚拟机扩展服务可帮助提供适用于 Windows 和 Linux 虚拟机的 IaaS 功能。  例如，可以使用 CRP 预配一个 Linux 虚拟机，并在部署期间运行 Bash 脚本来配置该 VM。

### <a name="network-rp"></a>网络 RP
网络资源提供程序 (NRP) 为私有云提供了一系列软件定义的网络 (SDN) 和网络功能虚拟化 (NFV) 功能。  NRP 可用于创建资源，如软件负载平衡器、 公共 Ip 网络安全组，虚拟网络。

### <a name="storage-rp"></a>存储 RP
存储 RP 提供四个 Azure 一致的存储服务：Blob、表、队列和帐户管理。 它还提供存储云管理服务，用于简化 Azure 一致的存储服务的服务提供程序管理。 Azure 存储空间提供的灵活性来存储和检索大量非结构化数据，如文档和使用 Azure Blob、 媒体文件和结构化的 NoSQL 基于 Azure 表的数据。 有关 Azure 存储的详细信息，请参阅 [Microsoft Azure 存储简介](../storage/common/storage-introduction.md)。

#### <a name="blob-storage"></a>Blob 存储
Blob 存储可存储任意数据集。 Blob 可以是任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。 表存储可存储结构化数据集。 表存储是一个 NoSQL“键-属性”数据存储，可以用于实现快速开发以及快速访问大量数据。 队列存储为云服务的各个组件之间的工作流处理和通信提供可靠的消息传送。

每个 Blob 组织在容器中。 容器还提供了一种有用的方式来向对象组分配安全策略。 一个存储帐户可以包含任意数目的容器，一个容器可以包含任意数目的 Blob，直至达到存储帐户的容量限制 500 TB。 Blob 存储提供三种类型的 Blob：块 Blob、追加 Blob 和页 Blob（磁盘）。 块 Blob 进行了相应的优化来流化和存储云对象，并且是用于存储文档、介质文件和备份等对象的不错选择。追加 Blob 类似于块 Blob，但针对追加追加操作进行了优化。 追加 Blob 仅可以通过将新的块添加到末尾来进行更新。 对于需要新数据只能写入到 Blob 结尾的情况，例如日志记录，追加 Blob 是一个不错的选择 。 页 blob 进行了优化来表示 IaaS 磁盘和支持随机写入，并且可以达 1 TB。 Azure 虚拟机网络连接的 IaaS 磁盘是一个 VHD，存储为页 Blob。

#### <a name="table-storage"></a>表存储
表存储是 Microsoft 的 NoSQL 键/属性存储 – 它采用无架构设计，因此不同于传统的关系数据库。 由于数据存储没有架构，因此可以随着应用程序需求的变化，使数据适应存储。 表存储易于使用，因此开发人员可以快速创建应用程序。 表存储是一种“键-属性”存储，这意味着表中的每个值都是随所键入的一个属性名称存储的。 属性名称可以用来筛选和指定选择条件。 属性集合及其值构成了实体。 由于表存储没有架构，因此同一表中的两个实体可以包含不同的属性集合，并且这些属性可以属于不同的类型。 可以使用表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，以及服务需要的任何其他类型的元数据。 可以在表中存储任意数量的实体，并且一个存储帐户可以包含任意数量的表，直至达到存储帐户的容量极限。

#### <a name="queue-storage"></a>队列存储
Azure 队列存储用于在应用程序组件之间进行云消息传送。 在设计应用程序以实现伸缩性时，通常要将各个应用程序组件分离，使其可以独立地进行伸缩。 队列存储提供的异步消息传送适用于在应用程序组件之间进行通信，无论这些应用程序组件是运行在云中、桌面上、本地服务器上还是移动设备上。 队列存储还支持管理异步任务以及构建过程工作流。

### <a name="keyvault"></a>KeyVault
KeyVault RP 针对密码和证书等机密提供管理与审核。 例如，在 VM 部署期间，租户可以使用 KeyVault RP 来提供管理员密码或密钥。

## <a name="high-availability-for-azure-stack"></a>Azure 堆栈的高可用性
*适用范围： Azure 堆栈 1802年或更高版本*

若要实现多 VM 生产系统在 Azure 中的高可用性，Vm 放置在一个可用性集，其中会将它们分布在多个容错域和更新域中。 这种方式，[在可用性集中部署的 Vm](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)是以物理方式相互隔离的单独服务器架下图中所示的情况下以便故障复原上：

  ![Azure 堆栈高可用性](media/azure-stack-key-features/high-availability.png)

### <a name="availablity-sets-in-azure-stack"></a>Azure 堆栈中的可用性集
已能够弹性应对故障 Azure 堆栈的基础结构时，基础技术 （故障转移群集） 仍会产生停机一段时间的 Vm 在受影响的物理服务器上出现硬件故障时。 Azure 堆栈支持具有可用性集最多为三个容错域与 Azure 相一致。

- **故障域**。 通过使它们尽可能均匀地分布于多个容错域 （Azure 堆栈节点），会以物理方式相互隔离的 Vm 放在一个可用性集中。 出现硬件故障，从失败的容错域的 Vm 将在其他容错域上，重新启动，但是，如果可能，在同一可用性集中其他 vm 保留在单独的容错域中。 当硬件重新联机时，Vm 将重新平衡，以维持高可用性。 
 
- **更新域**。 更新域是提供在可用性集中的高可用性的另一个 Azure 概念。 更新域是可以在同一时间执行维护的基础硬件的逻辑组。 计划内维护期间，将在一起启 Vm 位于同一更新域。 租户创建 Vm 在可用性集中，Azure 平台会自动将分布 Vm 到这些更新域。 在 Azure 堆栈 Vm 是实时更新其基础主机之前，在群集中其他联机主机之间迁移。 由于主机更新的过程中没有不租户停机的情况，Azure 堆栈上的更新域功能将仅存在于与 Azure 的模板兼容。 

### <a name="upgrade-scenarios"></a>升级方案 
Vm 在可用性集中创建之前 Azure 堆栈版本 1802年提供容错和更新域默认数 (1 和 1 分别)。 若要在这些预先存在的可用性集的 Vm 中实现高可用性，你必须首先删除现有的 Vm，然后将它们重新部署到新的可用性集与正确的容错和更新域计数中所述[更改为 Windows 的 VM 设置可用性](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set)。 

VM 缩放集，可用性集内部创建与默认容错域和更新域计数 (3 和 5 分别)。 任何 VM 缩放集创建之前 1802年更新将被放入可用性集与默认容错和更新域计数 (1 和 1 分别)。 若要更新这些 VM 缩放集实例，以实现较新的跨页，通过向外扩展 VM 缩放集的 1802年更新之前存在，然后删除 VM 缩放集的较旧实例的实例数。 

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)
可以使用 RBAC 向已获授权的用户、组和服务授予系统访问权限：在订阅、资源组或单个资源的级别为其分配角色即可。 每个角色定义了用户、组或服务对 Microsoft Azure Stack 资源拥有的访问级别。

Azure RBAC 具有适用于所有资源类型的三个基本角色： 所有者、 参与者和读取器。 “所有者”拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 “参与者”可以创建和管理所有类型的 Azure 资源，但不能将访问权限授予其他用户。 “读取者”只能查看现有 Azure 资源。 Azure 中的其他 RBAC 角色允许对特定的 Azure 资源进行管理。 例如，“虚拟机参与者”角色允许创建和管理虚拟机，但不允许管理虚拟机连接到的虚拟网络或子网。

## <a name="usage-data"></a>使用情况数据
Microsoft Azure 堆栈收集和使用数据聚合跨所有资源提供程序，并将其传输到 Azure 以进行处理通过 Azure 商务。 可以通过 REST API 收集 Azure 堆栈上的使用情况数据。 可以使用 Azure 一致的租户 API 以及提供程序和委派提供程序 API 从所有租户订阅获取使用情况数据。 可以使用这些数据来集成外部工具或服务，以实现计费或费用分摊。 后已通过 Azure 商务处理使用情况，可以在 Azure 的计费门户中查看它。

## <a name="in-development-build-of-azure-stack-development-kit"></a>在开发 Azure 堆栈开发工具包的内部版本
在开发内部版本让早期采用者评估 Azure 堆栈开发工具包的最新版本。 它们是基于最新主要版本的增量内部版本。 主要版本每隔几个月持续发布，开发中内部版本在主要版本的发布间隔期间歇性发布。

开发中内部版本提供以下好处：
- Bug 修复
- 新增功能
- 其他改进

## <a name="next-steps"></a>后续步骤
[Azure 堆栈部署先决条件](azure-stack-deploy.md)

