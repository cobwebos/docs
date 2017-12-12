---
title: "Azure 堆栈管理基础知识 |Microsoft 文档"
description: "了解你需要知道要管理 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: fa77faac195de3be7bf7b2785eb589b030a6e6ce
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-stack-administration-basics"></a>Azure 堆栈管理基础知识

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

有几种方法，你需要知道是否你不熟悉 Azure 堆栈管理。 本指南提供作为 Azure 堆栈操作员，你的角色的概述以及你需要告诉你为它们的用户，以快速提高工作效率。

## <a name="understand-the-builds"></a>了解生成

### <a name="integrated-systems"></a>集成系统

如果你使用 Azure 堆栈集成系统，通过更新包分发的 Azure 堆栈的更新的版本。 你可以导入这些程序包，并将其应用通过使用管理员门户中更新磁贴。
 
### <a name="development-kit"></a>开发工具包

如果你使用 Azure 堆栈开发工具包，查看[什么是 Azure 堆栈？](azure-stack-poc.md)文章，以确保你了解的用途开发工具包中，以及其自身的局限性。 你应使用作为"沙盒，"可以在其中评估 Azure 堆栈、 开发和非生产环境中测试你的应用的开发工具包。 (有关部署信息，请参阅[Azure 堆栈开发工具包部署](azure-stack-deploy-overview.md)快速入门。)

如 Azure，我们快速创新。 我们将定期发布新版本。 如果你正在运行的开发工具包和您想要移动到的最新的生成，你必须[重新部署 Azure 堆栈](azure-stack-redeploy.md)。 无法应用更新包。 此过程需要时间，但好处是，你还可以尝试的最新功能。 我们的网站上的开发工具包文档反映最新的发布版本。

## <a name="learn-about-available-services"></a>了解有关可用服务

你将需要的意识的哪些服务可提供给你的用户。 Azure 堆栈支持 Azure 服务的子集。 支持的服务的列表将继续改进。

**基本服务**

默认情况下，Azure 堆栈包括以下"基本服务"部署 Azure 堆栈时：

- 计算
- 存储
- 联网
- Key Vault

与这些基本的服务，你可以在你的用户以最小配置中提供基础结构作为-服务 (IaaS)。

**其他服务**

目前，我们支持以下的其他平台作为-服务 (PaaS) 服务：

- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库

你可以使它们可供你的用户之前，这些服务将需要其他配置。 有关详细信息，请参阅"教程"和我们 Azure 堆栈的运算符文档的"操作指南 guides\Offer 服务"部分。

**服务路线图**

Azure 堆栈将继续添加对 Azure 服务的支持。 预计的路线图，请参阅[Azure 堆栈： Azure 的扩展](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)白皮书。 你还可以监视[Azure 堆栈博客文章](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)的新公告。

## <a name="what-tools-do-i-use-to-manage"></a>要使用哪些工具来管理？
 
你可以使用[管理员门户](azure-stack-manage-portals.md)或 PowerShell 来管理 Azure 堆栈。 若要了解的基本概念的最简单方法是通过门户。 如果你想要使用 PowerShell，有准备步骤。 你必须[安装](azure-stack-powershell-install.md)PowerShell，[下载](azure-stack-powershell-download.md)其他模块和[配置](azure-stack-powershell-configure-admin.md)PowerShell。

Azure 堆栈使用 Azure 资源管理器作为其基础的部署、 管理和组织机制。 如果你要管理 Azure 堆栈和帮助支持用户，你应该了解有关资源管理器。 请参阅[Azure 资源管理器入门](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)白皮书。

## <a name="your-typical-responsibilities"></a>您典型的职责

你的用户想要使用服务。 从其角度来看，你的主要角色是使这些服务提供给他们。 你必须决定哪些服务提供，并使这些服务可通过创建计划、 服务和配额。 有关详细信息，请参阅[产品 Azure 堆栈中的服务的概述](azure-stack-offer-services-overview.md)。 

你还需要将项添加到[marketplace](azure-stack-marketplace.md)，如虚拟机映像。 最简单方法是到[从 Azure 应用商店项下载到 Azure 堆栈](azure-stack-download-azure-marketplace-item.md)。

> [!NOTE]
> 如果你想要测试计划、 服务和服务，则应使用[用户门户](azure-stack-manage-portals.md); 不管理员门户。

除了提供服务，你必须执行的所有正则职责的运算符需要启动并运行 Azure 堆栈。 这些任务包括：

- 添加用户帐户 (为[Azure Active Directory](azure-stack-add-new-user-aad.md)部署或[Active Directory 联合身份验证服务](azure-stack-add-users-adfs.md)部署)
- [分配基于角色的访问控制 (RBAC) 角色](azure-stack-manage-permissions.md)（这并不限于管理员。）
- [监视基础结构运行状况](azure-stack-monitor-health.md)
- 管理[网络](azure-stack-viewing-public-ip-address-consumption.md)和[存储](azure-stack-manage-storage-accounts.md)资源
- 将损坏的硬件，例如[替换故障的磁盘](azure-stack-replace-disk.md)。

## <a name="what-to-tell-your-users"></a>需要了解你的用户的内容

你将需要让用户知道如何使用 Azure 堆栈中的服务、 如何连接到环境，以及如何订阅产品。 除了任何自定义文档外，你可能想要提供你的用户，你可以将用户定向到 Azure 堆栈用户文档站点。

**了解如何使用 Azure 堆栈中的服务**

没有使用服务并生成 Azure 堆栈中的应用之前，必须了解你的用户的信息。 例如，没有特定的 PowerShell 和 API 版本要求。 此外，有一些功能增量 Azure 中的服务和 Azure 堆栈中的等效服务之间。 请确保你的用户查看以下文章：

- [关键注意事项： 使用服务或 Azure 堆栈为构建应用](user/azure-stack-considerations.md)
- [Azure 堆栈中的虚拟机的注意事项](user/azure-stack-vm-considerations.md)
- [存储： 差异和注意事项](user/azure-stack-acs-differences.md)

这些文章中的信息总结了 Azure 中的服务和 Azure 堆栈之间的差异。 它可用于全局 Azure 文档中的 Azure 服务的信息的补充。

**以用户身份连接到 Azure 堆栈**

在开发工具包环境中，如果用户不具有远程桌面访问开发工具包主机，它们必须配置虚拟专用网络 (VPN) 连接之后，才能访问 Azure 堆栈。 请参阅[连接到 Azure 的堆栈](azure-stack-connect-azure-stack.md)。 

你的用户将需要知道如何[访问用户门户](user/azure-stack-use-portal.md)或如何通过 PowerShell 连接。 在集成的系统环境中，每个部署而异的用户门户的地址。 你将需要为用户提供正确的 URL。

如果使用 PowerShell，用户可能需要注册资源提供程序，然后才能使用服务。 （资源提供程序管理服务。 例如，网络资源提供程序管理资源，例如虚拟网络、 网络接口和负载平衡器。)它们必须[安装](user/azure-stack-powershell-install.md)PowerShell，[下载](user/azure-stack-powershell-download.md)其他模块和[配置](user/azure-stack-powershell-configure-user.md)PowerShell （其中包括资源提供程序注册）。

**订阅产品/服务**

用户可以访问服务之前，它们必须[订阅产品](azure-stack-subscribe-plan-provision-vm.md)已作为操作员创建。

## <a name="where-to-get-support"></a>从何处获得支持

### <a name="integrated-systems"></a>集成系统

对于集成系统，没有协调的升级和 Microsoft 和我们原始设备制造商 (OEM) 的硬件合作伙伴之间的解析进程。

如果没有云服务问题，提供通过 Microsoft 客户支持服务 (CSS) 支持。 如果你的管理员门户中，右上角单击 （问号） 中的帮助和支持图标，然后单击**新建支持请求**，这将打开在其中你可以直接打开支持请求的站点。

如果没有部署的问题，修补程序和更新、 （包括现场可更换部件） 的硬件和任何硬件品牌的软件，如软件主机上运行硬件生命周期，请先联系 OEM 硬件供应商联系。

对于任何其他内容，请联系 Microsoft CSS。

### <a name="development-kit"></a>开发工具包

为开发工具包中，你可以要求中支持相关的问题[Microsoft 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)。 如果你的管理员门户中，右上角单击 （问号） 中的帮助和支持图标，然后单击**新建支持请求**，这将打开论坛站点直接。 定期监视这些论坛。 由于开发工具包是评估环境，没有通过 Microsoft CSS 提供正式支持。

## <a name="next-steps"></a>后续步骤

- [Azure 堆栈中的区域管理](azure-stack-region-management.md)


