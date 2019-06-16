---
title: FSLogix 配置文件的容器和 Windows 虚拟桌面-Azure 中的 Azure 文件
description: 本文介绍 FSLogix 中 Windows 虚拟桌面和 Azure 文件的容器，配置文件。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497540"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 配置文件容器和 Azure 文件

Windows 虚拟桌面预览服务建议作为用户配置文件解决方案 FSLogix 配置文件的容器。 FSLogix 旨在漫游配置文件在远程计算环境中，如 Windows 虚拟桌面。 它在单个容器中存储的完整用户配置文件。 在登录时，此容器将动态附加到在计算环境中使用本机支持的虚拟硬盘 (VHD) 和 HYPER-V 虚拟硬盘磁盘 (VHDX)。 用户配置文件是立即可用，并显示在完全相同的本机用户配置文件系统。

在本文中，我们将介绍 FSLogix 配置文件容器用于 Azure 文件。 Windows 虚拟桌面，这是上下文中的信息是[于 3 月 21 日宣布推出](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/)。

## <a name="user-profiles"></a>用户配置文件

用户配置文件包含有关个人，包括桌面设置、 持久网络连接等应用程序设置的配置信息的数据元素。 默认情况下，Windows 创建与操作系统紧密集成的本地用户配置文件。

远程用户配置文件提供了用户数据和操作系统之间分区。 它允许操作系统要替换或更改而不会影响用户数据。 在远程桌面会话主机 (RDSH) 和虚拟桌面基础结构 (VDI) 中，操作系统也可能由于以下原因替换为：

- 升级操作系统
- 替换的现有虚拟机 (VM)
- 用户正在共用 （非持久） RDSH 或 VDI 环境的一部分

用远程用户配置文件，包括这些技术的几种技术，Microsoft 产品进行操作：
- 漫游用户配置文件 (RUP)
- 用户配置文件磁盘 (UPD)
- 企业状态漫游 (ESR)

UPD 并 RUP 是远程桌面会话主机 (RDSH) 和虚拟硬盘 (VHD) 环境中的用户配置文件的使用最广泛的技术。

### <a name="challenges-with-previous-user-profile-technologies"></a>与上一个用户配置文件技术挑战

为用户配置文件的现有和旧版 Microsoft 解决方案总结了各种问题。 没有上一个解决方案处理附带 RDSH 或 VDI 环境的所有用户配置文件需求。 例如，UPD 无法处理较大的 OST 文件并 RUP 不保留最新设置。

#### <a name="functionality"></a>功能

下表显示了优势和限制的上一个用户配置文件技术。

| 技术 | 现代的设置 | Win32 设置 | OS 设置 | 用户数据 | 在服务器 SKU 上受支持 | 在 Azure 上的后端存储 | 后端存储在本地 | 版本支持 | 后续登录时间 |说明|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **用户配置文件磁盘 (UPD)** | 是 | 是 | 是 | 是 | 是 | 否 | 是 | Win 7 + | 是 | |
| **漫游用户配置文件 (RUP)，维护模式** | 否 | 是 | 是 | 是 | 是| 否 | 是 | Win 7 + | 否 | |
| **企业状态漫游 (ESR)** | 是 | 否 | 是 | 否 | 请参阅说明 | 是 | 否 | Win 10 | 否 | 基于服务器 SKU，但不支持用户界面的函数 |
| **用户体验虚拟化 (UE-V)** | 是 | 是 | 是 | 否 | 是 | 否 | 是 | Win 7 + | 否 |  |
| **OneDrive 云文件** | 否 | 否 | 否 | 是 | 请参阅说明 | 请参阅说明  | 请参阅说明 | Win 10 RS3 | 否 | 不在服务器 SKU 上测试。 在 Azure 上的后端存储空间取决于同步客户端。 后端存储在本地需要同步客户端。 |

#### <a name="performance"></a>性能

需要 UPD[存储空间直通 (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)以满足性能要求。 UPD 使用服务器消息块 (SMB) 协议。 它将该配置文件复制到在其中记录用户的 VM。 使用 S2D UPD 是 RDS 团队为 Windows 虚拟桌面预览期间，建议的服务的解决方案。  

#### <a name="cost"></a>成本

虽然 S2D 群集实现必要的性能，代价是适用于企业客户费用高昂，而且尤其是开销很大小型和中型企业 (SMB) 客户。 对于此解决方案，企业为高级存储磁盘，以及使用共享磁盘的 Vm 的成本付费。

#### <a name="administrative-overhead"></a>管理开销

S2D 群集需要操作系统修补、 更新和维护处于安全状态。 这些过程和设置 S2D 灾难恢复的复杂性使 S2D 可行仅对具有专用的 IT 员工的企业。

## <a name="fslogix-profile-containers"></a>FSLogix 配置文件容器

于 2018 年 11 月 19 日[Microsoft 收购 FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)。 FSLogix 解决了多个配置文件容器难题。 主要因素是：

- **性能：** [FSLogix 配置文件容器](https://fslogix.com/products/profile-containers)是高性能和解决性能问题，阻止了从历史上看缓存 exchange 模式。
- **OneDrive:** 不使用 FSLogix 配置文件的容器，OneDrive for Business 不支持在非持久 RDSH 或 VDI 环境中。 [OneDrive for Business 和 FSLogix 最佳实践](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices)描述它们的交互方式。 有关详细信息，请参阅[使用同步客户端上的虚拟桌面](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi)。
- **其他文件夹：** FSLogix 提供的功能来扩展用户配置文件以包括其他文件夹。

因为在收购之后 Microsoft 启动现有用户配置文件解决方案，如 UPD 中，替换 FSLogix 配置文件的容器。

## <a name="azure-files-integration-with-azure-active-directory"></a>Azure 文件与 Azure Active Directory 的集成

FSLogix 配置文件容器性能和功能，充分利用云。 在 2018 年 9 月 24 日，Microsoft Azure 文件宣布推出公共预览版[支持 Azure Active Directory 身份验证的 Azure 文件](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/)。 通过解决成本和管理开销，使用 Azure Active Directory 身份验证的 Azure 文件是在新的 Windows 虚拟机服务中的用户配置文件的高级解决方案。

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 虚拟桌面的最佳做法

Windows 虚拟桌面提供完全控制大小、 类型和正在使用的客户的虚拟机的计数。 有关详细信息，请参阅[什么是 Windows 虚拟桌面预览版？](https://docs.microsoft.com/azure/virtual-desktop/overview)。

若要确保您的 Windows 虚拟桌面环境，请遵循最佳做法：

- Azure 文件存储帐户必须是会话主机 Vm 所在的同一区域中。
- Azure 文件权限应与中所述的权限相匹配[要求的配置文件容器](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers)。
- 每个主机池必须生成相同的类型和大小基于相同的主映像的 VM。
- 每个主机池 VM 必须在相同的资源组，以帮助管理、 缩放和更新。
- 为了获得最佳性能的存储解决方案和 FSLogix 配置文件容器应位于同一数据中心位置。
- 包含主映像的存储帐户必须是相同的区域和其中预配 Vm 的订阅中。

## <a name="next-steps"></a>后续步骤

使用以下说明来设置 Windows 虚拟桌面环境。

- 若要开始构建您的桌面虚拟化解决方案，请参阅[在 Windows 虚拟桌面中创建一个租户](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)。
- 若要创建 Windows 虚拟桌面租户中的主机池，请参阅[使用 Azure Marketplace 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)。
- 若要设置完全托管文件共享在云中，请参阅[设置 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable)。
- 若要配置 FSLogix 配置文件的容器，请参阅[主机池将用户配置文件共享设置](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile)。
- 若要将用户分配到主机池，请参阅[管理的 Windows 虚拟桌面应用程序组](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups)。
- 若要从 web 浏览器访问你的 Windows 虚拟机资源，请参阅[连接到 Windows 虚拟桌面](https://docs.microsoft.com/azure/virtual-desktop/connect-web)。
