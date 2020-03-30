---
title: Windows 虚拟桌面 FSLogix 配置文件容器文件 - Azure
description: 本文介绍 Windows 虚拟桌面和 Azure 文件中的 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127883"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 配置文件容器和 Azure 文件

Windows 虚拟桌面服务建议将 FSLogix 配置文件容器作为用户配置文件解决方案。 FSLogix 设计用于在远程计算环境中（如 Windows 虚拟桌面）中漫游配置文件。 它将完整的用户配置文件存储在单个容器中。 登录时，此容器使用本机支持的虚拟硬盘 （VHD） 和超 V 虚拟硬盘 （VHDX） 动态连接到计算环境。 用户配置文件立即可用，并且与本机用户配置文件完全一样显示在系统中。 本文介绍与 Azure 文件一起使用的 FSLogix 配置文件容器在 Windows 虚拟桌面中如何运行。

>[!NOTE]
>如果要查找有关 Azure 上不同 FSLogix 配置文件容器存储选项的比较材料，请参阅[FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="user-profiles"></a>用户配置文件

用户配置文件包含有关个人的数据元素，包括配置信息，如桌面设置、持久网络连接和应用程序设置。 默认情况下，Windows 会创建与操作系统紧密集成的本地用户配置文件。

远程用户配置文件在用户数据和操作系统之间提供分区。 它允许更换或更改操作系统，而不会影响用户数据。 在远程桌面会话主机 （RDSH） 和虚拟桌面基础结构 （VDI） 中，操作系统可能会由于以下原因被替换：

- 操作系统的升级
- 替换现有虚拟机 （VM）
- 用户是池式（非持久性）RDSH 或 VDI 环境的一部分

Microsoft 产品使用多种技术用于远程用户配置文件，包括以下技术：
- 漫游用户配置文件 （RUP）
- 用户配置文件磁盘 （UPD）
- 企业状态漫游 （ESR）

UPD 和 RUP 是远程桌面会话主机 （RDSH） 和虚拟硬盘 （VHD） 环境中用户配置文件使用最广泛的技术。

### <a name="challenges-with-previous-user-profile-technologies"></a>以前用户配置文件技术的挑战

针对用户配置文件的现有和遗留的 Microsoft 解决方案带来了各种挑战。 没有以前的解决方案处理 RDSH 或 VDI 环境附带的所有用户配置文件需求。 例如，UPD 无法处理大型 OST 文件，RUP 不会保留现代设置。

#### <a name="functionality"></a>功能

下表显示了以前用户配置文件技术的优点和限制。

| 技术 | 现代设置 | Win32 设置 | 操作系统设置 | 用户数据 | 支持服务器 SKU | Azure 上的后端存储 | 本地后端存储 | 版本支持 | 后续登录时间 |说明|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **用户配置文件磁盘 （UPD）** | 是 | 是 | 是 | 是 | 是 | 否 | 是 | 赢 7+ | 是 | |
| **漫游用户配置文件 （RUP），维护模式** | 否 | 是 | 是 | 是 | 是| 否 | 是 | 赢 7+ | 否 | |
| **企业状态漫游 （ESR）** | 是 | 否 | 是 | 否 | 请参阅说明 | 是 | 否 | Win 10 | 否 | 服务器 SKU 上的功能，但没有支持用户界面 |
| **用户体验虚拟化 (UE-V)** | 是 | 是 | 是 | 否 | 是 | 否 | 是 | 赢 7+ | 否 |  |
| **OneDrive 云文件** | 否 | 否 | 否 | 是 | 请参阅说明 | 请参阅说明  | 请参阅注释 | 赢得 10 RS3 | 否 | 未在服务器 SKU 上测试。 Azure 上的后端存储取决于同步客户端。 后端存储上预存储需要同步客户端。 |

#### <a name="performance"></a>性能

UPD 要求[存储空间直接 （S2D）](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)来满足性能要求。 UPD 使用服务器消息块 （SMB） 协议。 它将配置文件复制到正在记录用户的 VM。 使用 S2D 的 UPD 是我们建议用于 Windows 虚拟桌面的解决方案。  

#### <a name="cost"></a>成本

虽然 S2D 群集实现了必要的性能，但对于企业客户来说，成本是昂贵的，但对于中小型企业 （SMB） 客户来说尤其昂贵。 对于此解决方案，企业会为存储磁盘付费，以及使用磁盘进行共享的 VM 的成本。

#### <a name="administrative-overhead"></a>管理开销

S2D 群集需要以安全状态进行修补、更新和维护的操作系统。 这些流程和设置 S2D 灾难恢复的复杂性使得 S2D 仅适用于具有专职 IT 人员的企业。

## <a name="fslogix-profile-containers"></a>FSLogix 配置文件容器

2018年11月19日，[微软收购了FSLogix。](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) FSLogix 解决了许多配置文件容器挑战。 其中的关键是：

- **性能：**[FSLogix 配置文件容器](/fslogix/configure-profile-container-tutorial/)具有高性能，可解决历史上阻止缓存交换模式的性能问题。
- **OneDrive：** 如果没有 FSLogix 配置文件容器，在非持久性 RDSH 或 VDI 环境中不支持 OneDrive 业务。 [OneDrive 业务和 FSLogix 最佳实践](/fslogix/overview/)描述了它们如何交互。 有关详细信息，请参阅[在虚拟桌面上使用同步客户端](/deployoffice/rds-onedrive-business-vdi/)。
- **其他文件夹：** FSLogix 提供扩展用户配置文件以包括其他文件夹的功能。

自收购以来，微软开始用FSLogix配置文件容器替换现有的用户配置文件解决方案（如 UPD）。

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure 文件与 Azure 活动目录域服务集成

FSLogix 配置文件容器的性能和功能利用了云。 2019 年 8 月 7 日，Microsoft Azure 文件宣布[Azure 文件身份验证的通用版，Azure 活动目录域服务 （AD DS）](../storage/files/storage-files-active-directory-overview.md). 通过解决成本和管理开销，Azure 文件与 Azure AD DS 身份验证是 Windows 虚拟桌面服务中用户配置文件的高级解决方案。

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 虚拟桌面的最佳做法

Windows 虚拟桌面可完全控制客户正在使用的 VM 的大小、类型和计数。 有关详细信息，请参阅什么是[Windows 虚拟桌面？](overview.md)

为确保 Windows 虚拟桌面环境遵循最佳实践，请：

- Azure 文件存储帐户必须与会话主机 VM 位于同一区域。
- Azure 文件权限应与["要求 - 配置文件容器](/fslogix/fslogix-storage-config-ht)"中描述的权限匹配。
- 每个主机池必须基于同一主映像构建相同类型和大小的 VM。
- 每个主机池 VM 必须位于同一资源组中，以帮助管理、扩展和更新。
- 为获得最佳性能，存储解决方案和 FSLogix 配置文件容器应位于同一数据中心位置。
- 包含主映像的存储帐户必须位于预配 VM 的同一区域和订阅中。

## <a name="next-steps"></a>后续步骤

使用以下指南设置 Windows 虚拟桌面环境。

- 要开始构建桌面虚拟化解决方案，请参阅[在 Windows 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)。
- 要在 Windows 虚拟桌面租户中创建主机池，请参阅[使用 Azure 应用商店创建主机池](create-host-pools-azure-marketplace.md)。
- 要在云中设置完全托管的文件共享，请参阅[设置 Azure 文件共享](/azure/storage/files/storage-files-active-directory-enable/)。
- 要配置 FSLogix 配置文件容器，请参阅[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
- 要将用户分配给主机池，请参阅[管理 Windows 虚拟桌面的应用组](manage-app-groups.md)。
- 要从 Web 浏览器访问 Windows 虚拟桌面资源，请参阅[连接到 Windows 虚拟桌面](connect-web.md)。
