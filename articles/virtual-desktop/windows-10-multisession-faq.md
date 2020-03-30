---
title: Windows 10 企业多会话常见问题 - Azure
description: 为 Windows 虚拟桌面使用 Windows 10 企业多会话的常见问题和最佳实践。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4301aaed0152e07eb2a941b56637239b66c33878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127351"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 Enterprise 多会话常见问题解答

本文回答了常见问题，并解释了 Windows 10 企业多会话的最佳做法。
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>什么是 Windows 10 企业多会话？

Windows 10 企业多会话（以前称为适用于虚拟桌面的 Windows 10 企业版 ）是一个新的远程桌面会话主机，允许多个并发交互式会话。 以前，只有 Windows 服务器可以执行此操作。 此功能为用户提供了熟悉的 Windows 10 体验，而 IT 可以从多会话的成本优势中获益，并使用现有的每个用户 Windows 许可，而不是 RDS 客户端访问许可证 （CAL）。 有关许可证和定价的详细信息，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>有多少用户可以同时在 Windows 10 企业多会话上同时具有交互式会话？

可以同时处于活动状态的交互式会话有多少依赖于系统的硬件资源（vCPU、内存、磁盘和 vGPU）、用户在登录到会话时如何使用其应用以及系统工作负载的繁重程度。 我们建议您验证系统的性能，以了解在 Windows 10 企业多会话中可以拥有多少用户。 要了解更多信息，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>为什么我的应用程序将 Windows 10 企业多会话报告为服务器操作系统？

Windows 10 企业多会话是 Windows 10 企业版的虚拟版本。 其中一个区别是，此操作系统 （OS） 报告[ProductType](/windows/win32/cimwin32prov/win32-operatingsystem)的值为 3，与 Windows 服务器的值相同。 此属性使操作系统与现有的 RDSH 管理工具、RDSH 多会话感知应用程序以及 RDSH 环境的低级系统性能优化兼容。 某些应用程序安装程序可以阻止 Windows 10 多会话上的安装，具体取决于它们是否检测到 ProductType 设置为客户端。 如果应用无法安装，请与应用程序供应商联系以获取更新的版本。 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>是否可以在本地运行 Windows 10 企业多会话？

Windows 10 企业多会话无法在本地生产环境中运行，因为它针对 Azure 的 Windows 虚拟桌面服务进行了优化。 出于生产目的在 Azure 外部运行 Windows 10 企业多会话是违反许可协议的。 Windows 10 企业多会话不会针对本地密钥管理服务 （KMS） 激活。
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>如何为我的组织自定义 Windows 10 企业多会话映像？

您可以使用 Windows 10 Windows 10 企业多会话在 Azure 中启动虚拟机 （VM），并通过安装 LOB 应用程序、sysprep/通用化，然后使用 Azure 门户创建映像来自定义它。  
 
要开始，请使用 Windows 10 Windows 10 企业多会话在 Azure 中创建 VM。 可以直接下载 VHD，而不是在 Azure 中启动 VM。 之后，您将能够使用下载的 VHD 在启用了 Hyper-V 的 Windows 10 PC 上创建新的第 1 代 VM。

通过安装 LOB 应用程序和系统准备映像，根据您的需要自定义映像。 完成自定义后，将映像上载到 Azure，其中包含 VHD。 之后，从 Azure 应用商店获取 Windows 虚拟桌面，并用它来部署具有自定义映像的新主机池。
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>部署后如何管理 Windows 10 企业多会话？

您可以使用任何受支持的配置工具，但我们建议配置管理器 版本 1906，因为它支持 Windows 10 企业多会话。 我们目前正在开发微软 Intune 支持。
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 企业多会话是否可以加入 Azure 活动目录 （AD）？

Windows 10 企业多会话当前受支持为混合 Azure AD 加入。 在 Windows 10 企业多会话加入域后，使用现有的组策略对象启用 Azure AD 注册。 有关详细信息，请参阅[规划混合 Azure 活动目录联接实现](../active-directory/devices/hybrid-azuread-join-plan.md)。
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>在哪里可以找到 Windows 10 企业多会话映像？

Windows 10 企业多会话位于 Azure 库中。 要找到它，导航到 Azure 门户并搜索适用于虚拟桌面的 Windows 10 企业版。 有关与 Office Pro Plus 集成的图像，请访问 Azure 门户并搜索 Microsoft Windows 10 + Office 365 专业增强版。

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>我应该使用哪种 Windows 10 企业多会话映像？

Azure 库有多个版本，包括 Windows 10 企业多会话、版本 1809 和 Windows 10 企业多会话版本 1903。 我们建议使用最新版本来提高性能和可靠性。
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>支持哪些 Windows 10 企业多会话版本？

Windows 10 企业多会话版本 1809 及更高版本受支持，可在 Azure 库中使用。 这些版本遵循与 Windows 10 企业版相同的支持生命周期策略，这意味着春季版本支持 18 个月，秋季版本支持 30 个月。
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>我应该使用哪种配置文件管理解决方案用于 Windows 10 企业多会话？

我们建议您在非持久性环境或其他需要集中存储配置文件的方案中配置 Windows 10 企业版时使用 FSLogix 配置文件容器。 FSLogix 确保每个用户会话的用户配置文件可用和最新。 我们还建议使用 FSLogix 配置文件容器将用户配置文件存储在具有适当权限的任何 SMB 共享中，但如有必要，可以在 Azure 页 Blob 存储中存储用户配置文件。 Windows 虚拟桌面用户可以免费使用 FSLogix。
 
有关如何配置 FSLogix 配置文件容器的详细信息，请参阅[配置 FSLogix 配置文件容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)。  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>访问 Windows 10 企业多会话需要哪个许可证？

有关适用许可证的完整列表，请参阅[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>为什么我的应用在注销后会消失？

这是因为您使用 Windows 10 企业多会话，具有配置文件管理解决方案（如 FSLogix）。 您的管理员或配置文件解决方案配置了您的系统，以便用户注销时删除用户配置文件。此配置意味着，当您的系统在注销后删除用户配置文件时，系统还会删除您在会话期间安装的任何应用。 如果要保留已安装的应用，则需要要求管理员为 Windows 虚拟桌面环境中的所有用户预配这些应用。

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>如何确保应用在用户注销时不会消失？

默认情况下，大多数虚拟化环境都进行了配置，以防止用户将其他应用安装到其配置文件中。 如果要确保应用在用户退出 Windows 虚拟桌面时不会消失，则必须为环境中的所有用户配置文件预配该应用。 有关预配应用的详细信息，请查看以下资源：

- [在 Windows 虚拟桌面中发布内置应用](publish-apps.md)
- [DISM 应用包服务命令行选项](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [附加应用预配包](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>如何确保用户不会从 Microsoft 应用商店下载和安装应用？

您可以禁用 Microsoft 应用商店应用，以确保用户不会下载超出已为其预配的应用之外的额外应用。

要禁用应用商店应用，

1. 创建新的组策略。
2. 选择**计算机配置** > **管理模板** > **窗口组件**。
3. 选择“应用商店”。****
4. 选择**存储应用程序**。
5. 选择 **"已禁用**"，然后选择 **"确定**"。
6. 选择“应用”。
 
## <a name="next-steps"></a>后续步骤

要了解有关 Windows 虚拟桌面和 Windows 10 企业多会话的更多信息，：

- 阅读我们的[Windows 虚拟桌面预览文档](overview.md)
- 访问我们的[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- 使用[Windows 虚拟桌面教程](tenant-setup-azure-active-directory.md)设置 Windows 虚拟桌面部署
