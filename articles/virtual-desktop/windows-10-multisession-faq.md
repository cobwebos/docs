---
title: Windows 10 企业多会话常见问题-Azure
description: 有关使用 windows 10 企业多会话 Windows 虚拟桌面的常见问题和最佳实践。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: helohr
ms.openlocfilehash: f1ba54547b947e18d2d42520c0fb51a0855fb37c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901603"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 Enterprise 多会话常见问题解答

本文将回答一些常见问题，并涵盖 Windows 10 企业多会话的最佳实践。
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>什么是 Windows 10 企业多会话？ 

Windows 10 企业多会话（以前称为 Windows 10 企业版虚拟桌面（EVD））是一个新的远程桌面会话主机，它允许以前只有 Windows Server 才能完成的多个并发交互会话。 此功能为用户提供了一个熟悉的 Windows 10 体验，同时它可以从多会话的成本优势和使用现有的每用户 Windows 授权（而不是 RDS 客户端访问许可证（Cal））中受益。 有关许可证和定价的详细信息，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>有多少用户可以在 Windows 10 企业多会话上同时拥有交互式会话？

可以同时处于活动状态的交互式会话的数量取决于系统的硬件资源（vCPU、内存、磁盘和 vGPU）、用户在登录到会话时使用其应用的方式，以及系统的工作负载。 建议你验证系统的性能，以了解你可以在 Windows 10 企业多会话上拥有的用户数量。 若要了解详细信息，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>为什么应用程序报告 Windows 10 企业多会话作为服务器操作系统？

Windows 10 企业多会话是 Windows 10 企业版的虚拟版本。 其中一项差异是，此操作系统（OS）会将值为3的[ProductType](https://docs.microsoft.com/windows/desktop/cimwin32prov/win32-operatingsystem)报告为与 Windows Server 相同的值。 此属性可保持操作系统与现有的 RDSH 管理工具、RDSH 多会话感知应用程序的兼容性，并且主要用于 RDSH 环境的低级别系统性能优化。 某些应用程序安装程序可以在 Windows 10 多会话上阻止安装，具体取决于它们是否检测到 "ProductType" 设置为 "客户端"。 如果你的应用程序无法安装，请与你的应用程序供应商联系以获取更新的版本。 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>能否在本地运行 Windows 10 企业多会话？

Windows 10 企业多会话无法在本地生产环境中运行，因为它已针对 Azure 的 Windows 虚拟桌面服务进行了优化。 本指南针对生产目的在 Azure 外部运行 Windows 10 企业多会话的许可协议。 Windows 10 企业多会话不会针对本地密钥管理服务（KMS）激活。
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>如何实现为组织自定义 Windows 10 企业多会话映像？

你可以在 Azure 中使用 Windows 10 Windows 10 企业多会话启动虚拟机（VM），然后通过安装 LOB 应用程序、sysprep/通用化，并使用 Azure 门户创建映像来对其进行自定义。  
 
若要开始，请使用 Windows 10 Windows 10 企业多会话在 Azure 中创建 VM。 你可以直接下载 VHD，而不是在 Azure 中启动 VM。 之后，你将能够使用已下载的 VHD 在启用 Hyper-v 的 Windows 10 电脑上创建新的第1代 VM。

通过安装 LOB 应用程序并 sysprep 映像，根据需要自定义映像。 完成自定义后，将映像上传到 Azure 中的 VHD。 然后，从 Azure Marketplace 获取 Windows 虚拟桌面，并将其用于使用自定义映像部署新的主机池。
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>部署后如何实现管理 Windows 10 企业多会话？

你可以使用任何支持的配置工具，但建议使用 System Center Configuration Manager 1906，因为它支持 Windows 10 企业多会话。 目前正在 Microsoft Intune 支持。
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 企业多会话是否可以 Azure Active Directory （AD）加入？

目前支持 Windows 10 企业多会话 Azure AD 联接混合。 在 Windows 10 企业多会话已加入域后，使用现有组策略对象启用 Azure AD 注册。 有关详细信息，请参阅[计划混合 Azure Active Directory 联接实现](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)。
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>在哪里可以找到 Windows 10 企业多会话映像？

Windows 10 企业多会话位于 Azure 库中。 若要找到它，请导航到 Azure 门户并搜索 Windows 10 Enterprise for Virtual 台式电脑版本。 对于与 Office Pro Plus 集成的图像，请参阅 "Azure 门户"，然后搜索 "Microsoft Windows 10 + Office 365 ProPlus"。

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>我应该使用哪个 Windows 10 企业多会话映像？

Azure 库包含多个版本，包括 Windows 10 企业多会话、版本1809和 Windows 10 企业多会话版本1903。 建议使用最新版本，提高性能和可靠性。
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>支持哪些 Windows 10 企业多会话版本？

Windows 10 企业多会话版本1809及更高版本都受支持，并在 Azure 库中提供。 这些版本遵循与 Windows 10 企业版相同的支持生命周期策略，这意味着该春季版本支持18个月，而秋季发行版为30个月。
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>对于 Windows 10 企业多会话，应该使用哪种配置文件管理解决方案？

如果在非持久性环境中配置 Windows 10 企业版或需要集中存储配置文件的其他方案，建议使用 FSLogix 配置文件容器。 FSLogix 确保用户配置文件可用，并为每个用户会话提供最新信息。 我们还建议你使用 FSLogix 配置文件容器将用户配置文件存储在具有相应权限的任何 SMB 共享中，但如果需要，你可以将用户配置文件存储在 Azure 页 blob 存储中。 Windows 虚拟桌面用户可以使用 FSLogix，无需额外付费。
 
有关如何配置 FSLogix 配置文件容器的详细信息，请参阅[配置 FSLogix 配置文件容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)。  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>访问 Windows 10 企业多会话需要哪些许可证？

有关适用许可证的完整列表，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。
 
## <a name="next-steps"></a>后续步骤

若要了解有关 Windows 虚拟桌面和 Windows 10 企业多会话的详细信息：

- 阅读我们的[Windows 虚拟桌面预览版文档](overview.md)
- 访问我们的[Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- 通过[Windows 虚拟桌面教程](tenant-setup-azure-active-directory.md)设置 Windows 虚拟桌面部署
