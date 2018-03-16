---
title: "Azure 词汇表 - Azure 字典 | Microsoft Docs"
description: "使用 Azure 词汇表来理解 Azure 平台上的云术语。 这份简短的 Azure 字典提供 Azure 通用云术语的定义。"
keywords: "Azure 字典, 云术语, Azure 词汇表, 术语定义, 云名词"
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: 
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: fd20405c284161a1a66493056f21c147efa632cb
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure 词汇表：Azure 平台上的云术语字典

Microsoft Azure 词汇表是适用于 Azure 平台的简短云术语字典。 另请参阅：

* [Microsoft Azure 和 Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) - Azure 服务及其 AWS 对等项的定义。<!-- I propose to link to https://azure.microsoft.com/en-us/services/ instead of this -->
* [云计算术语](https://azure.microsoft.com/overview/cloud-computing-dictionary/) - 常规行业云术语。

## <a name="account"></a>帐户
用于访问和管理 Azure 订阅的帐户。 它通常被称为 Azure 帐户，尽管帐户可以是以下任何一个：现有的工作、学校或个人 Microsoft 帐户，或 Office 365 用户名和密码。 当注册[免费试用版](https://azure.microsoft.com)时，还可以创建一个帐户来管理 Azure 订阅。  
请参阅[使用 Office 365 帐户注册 Azure 订阅](billing/billing-use-existing-office-365-account-azure-subscription.md)和[可用于登录的帐户](active-directory/active-directory-how-subscriptions-associated-directory.md)。

## <a name="api-app"></a>API 应用
又叫[应用服务应用](#app-service-app)。

## <a name="app-service-app"></a>应用服务应用
[Azure 应用服务](app-service/app-service-web-overview.md)提供的用于托管网站或 Web 应用程序、Web API 或[移动应用后端](app-service-mobile/app-service-mobile-value-prop.md)的计算资源。 应用服务应用也称为“应用服务”、“Web 应用”、“API 应用”和“移动应用”。

## <a name="availability-set"></a>可用性集
可一起管理的、用于提供应用程序冗余和可靠性的虚拟机集合。 使用可用性集可确保在计划内或计划外维护事件期间，至少有一个虚拟机可用。  
请参阅[管理 Windows 虚拟机的可用性](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)和[管理 Linux 虚拟机的可用性](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Azure 经典部署模型
用于在 Azure 中部署资源的两个[部署模型](resource-manager-deployment-model.md)中的一个（新模型是 Azure 资源管理器）。 有些 Azure 服务仅支持 Resource Manager 部署模型，有些仅支持经典部署模型，而有些则两种模型都支持。 每个 Azure 服务的文档中指定了其支持的模型。

## <a name="cli"></a>Azure 命令行接口 (CLI)
一个命令行接口，可用于从 Windows、macOS 和 Linux 管理 Azure 服务。  某些服务或服务功能只能通过 PowerShell 或 CLI 来管理。 请参阅 [Azure CLI 2.0](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
一个命令行接口，可用于从 Windows 电脑通过命令行管理 Azure 服务。 某些服务或服务功能只能通过 PowerShell 或 CLI 来管理。
请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Azure 资源管理器部署模型
用于在 Microsoft Azure 中部署资源的两个[部署模型](resource-manager-deployment-model.md)中的一个（另一个是经典部署模型）。 有些 Azure 服务仅支持 Resource Manager 部署模型，有些仅支持经典部署模型，而有些则两种模型都支持。 每个 Azure 服务的文档中指定了其支持的模型。

## <a name="fault-domain"></a>容错域
可用性集中可能会同时发生故障的虚拟机集合。 一个例子是机架中共用一个电源和网络交换机的计算机组。 在 Azure 中，可用性集内的虚拟机会自动分布在多个容错域中。  
请参阅[管理 Windows 虚拟机的可用性](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或[管理 Linux 虚拟机的可用性](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>地区
针对数据驻留位置定义的边界，通常包含两个或更多个区域。 边界可能在国界内或超出国界，并受到税法影响。 每个地区至少有一个区域。 地区的示例包括亚太区和日本。 也称为*地理位置*。  
请参阅 [Azure 区域](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>异地复制
在区域对中自动复制内容（例如 Blob、表和队列）的过程。  
请参阅 [Azure SQL 数据库的活动异地复制](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>图像
包含操作系统和应用程序配置的文件，可用于创建任意数目的虚拟机。 Azure 中有两种类型的映像：VM 映像和 OS 映像。 VM 映像包括在创建该映像时附加到虚拟机的操作系统和所有磁盘。 OS 映像仅包含通用化操作系统，而不包含任何数据磁盘配置。  
请参阅[使用 PowerShell 或 CLI 在 Azure 中导航和选择 Windows 虚拟机映像](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>限制
可以创建的资源数目或可实现的性能基准。 限制通常与订阅、服务和产品相关联。  
请参阅 [Azure 订阅和服务限制、配额和约束](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>负载均衡器
在网络中的计算机之间分散传入流量的资源。 在 Azure 中，负载均衡器将流量分散到负载均衡器集内定义的虚拟机。 [负载均衡器](load-balancer/load-balancer-overview.md)可以面向 Internet，也可以是内部的。  

## <a name="mobile-app"></a>移动应用
又叫[应用服务应用](#app-service-app)。

## <a name="offer"></a>offer
适用于 Azure 订阅的定价、信用额度和相关条款。  
请参阅 [Azure 产品/服务详细信息页](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
用于部署和管理 Azure 服务的安全 Web 门户。

## <a name="region"></a>region
某个地区内不跨国界且包含一个或多个数据中心的区域。 定价、区域性服务和优惠类型在区域级别公开。 一个区域通常与另一个区域配对，两者距离最多可达数百英里。 区域对可用作灾难恢复和高可用性方案的机制。 也称为位置。  
请参阅 [Azure 区域](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resource
属于 Azure 解决方案的一部分的项。 每个 Azure 服务可让你部署不同类型的资源，例如数据库或虚拟机。   
请参阅 [Azure 资源管理器概述](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>资源组
Resource Manager 中的容器，用于保存应用程序的相关资源。 资源组可以包含应用程序的所有资源，也可以只包含逻辑分组在一起的资源。 可以根据对组织有利的原则，决定如何将资源分配到资源组。  
请参阅 [Azure 资源管理器概述](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>资源管理器模板
一个 JSON 文件，它以声明方式定义一个或多个 Azure 资源，并定义所部署资源之间的依赖关系。 使用模板能够以一致方式反复部署资源。  
请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>资源提供程序
一种服务，提供可以通过 Resource Manager 进行部署和管理的资源。 每个资源提供程序提供用于处理所部署资源的操作。 可以通过 Azure 门户、Azure PowerShell 和多个编程 SDK 来访问资源提供程序。  
请参阅 [Azure 资源管理器概述](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>role
用于控制可分配给用户、组和服务的访问权限的方式。 角色能够对 Azure 资源执行创建、管理和读取等操作。  
请参阅 [RBAC：内置角色](active-directory/role-based-access-built-in-roles.md)

## <a name="sla"></a>服务级别协议 (SLA)
用于描述 Microsoft 在运行时间和连接性方面所做承诺的协议。 每个 Azure 服务都有具体的 SLA。  
请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>共享访问签名 (SAS)
使用该签名能够在不公开帐户密钥的情况下，授予对资源的有限访问权限。 例如，[Azure 存储使用 SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) 授予客户端对 Blob 等对象的访问权限。 [IoT 中心使用 SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) 授予设备发送遥测数据的权限。

## <a name="storage-account"></a>存储帐户
该帐户可授予对 Azure 存储中的 Azure Blob、队列、表和文件服务的访问权限。 存储帐户名称定义 Azure 存储数据对象的唯一命名空间。  
请参阅[关于 Azure 存储帐户](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>订阅
客户与 Microsoft 之间的协议，可让客户获取 Azure 服务。 订阅定价和相关条款受为订阅选择的产品/服务约束。
请参阅 [Microsoft 在线订阅协议](https://azure.microsoft.com/support/legal/subscription-agreement/)和 [Azure 订阅与 Azure Active Directory 的关联方式](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>标记
一个索引编制术语，让你根据管理或计费要求为资源分类。 如果有一系列复杂的资源，则可以使用标记以最有利的方式可视化这些资产。 例如，可以标记组织中充当类似角色或者属于同一部门的资源。  
请参阅[使用标记整理 Azure 资源](resource-group-using-tags.md)

## <a name="update-domain"></a>更新域
可用性集中可同时更新的虚拟机集合。 同一个更新域中的虚拟机会在计划维护期间一起重新启动。 Azure 永远不会一次性重新启动多个更新域。 也称为升级域。  
请参阅[管理 Windows 虚拟机的可用性](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)和[管理 Linux 虚拟机的可用性](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>虚拟机
运行操作系统的物理计算机的软件实现。 多个虚拟机可以在同一个硬件上同时运行。 在 Azure 中，虚拟机具有不同的大小。  
请参阅[虚拟机文档](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>虚拟机扩展
一个实现行为或功能的资源，可帮助其他程序正常工作，或提供与运行中计算机交互的能力。 例如，可以使用 VM 访问扩展来重置或修改 Azure 虚拟机上的远程访问值。
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
请参阅[关于虚拟机扩展和功能 (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或[关于虚拟机扩展和功能 (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>虚拟网络
在 Azure 资源之间提供连接并与其他所有 Azure 租户隔离的网络。 一个[Azure VPN 网关](vpn-gateway/vpn-gateway-about-vpngateways.md)，使用它可以在虚拟网络之间以及[虚拟网络与本地网络之间](vpn-gateway/vpn-gateway-plan-design.md)建立连接。 可以完全控制虚拟网络中的 IP 地址块、DNS 设置、安全策略和路由表。  
请参阅[虚拟网络概述](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Web 应用
又叫[应用服务应用](#app-service-app)。

## <a name="see-also"></a>另请参阅

* [Azure 入门](https://azure.microsoft.com/get-started/)
* [云资源中心](https://azure.microsoft.com/resources/)  
* [适用于业务应用程序的 Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [数据中心的 Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)

