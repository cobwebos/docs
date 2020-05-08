---
title: Windows 虚拟桌面 FSLogix 配置文件容器文件-Azure
description: 本文介绍 Windows 虚拟桌面和 Azure 文件中的 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7728ff96ccc3da5a36d919e61518a3ce3d13581c
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611970"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 配置文件容器和 Azure 文件

Windows 虚拟桌面服务建议将 FSLogix 配置文件容器作为用户配置文件解决方案。 FSLogix 设计用于在远程计算环境（如 Windows 虚拟桌面）中漫游配置文件。 它将完整的用户配置文件存储在单个容器中。 登录时，将使用本机支持的虚拟硬盘（VHD）和 Hyper-v 虚拟硬盘（VHDX）将此容器动态连接到计算环境。 用户配置文件立即可用，并与本机用户配置文件完全相同。 本文介绍如何在 Windows 虚拟桌面中使用 FSLogix 配置文件容器与 Azure 文件一起工作。

>[!NOTE]
>如果正在查找有关 Azure 上不同 FSLogix 配置文件容器存储选项的比较资料，请参阅[FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="user-profiles"></a>用户配置文件

用户配置文件包含有关个人的数据元素，包括桌面设置、永久性网络连接和应用程序设置等配置信息。 默认情况下，Windows 将创建与操作系统紧密集成的本地用户配置文件。

远程用户配置文件在用户数据和操作系统之间提供分区。 它允许在不影响用户数据的情况下替换或更改操作系统。 在远程桌面会话主机（RDSH）和虚拟桌面基础结构（VDI）中，操作系统可能因以下原因而被替换：

- 操作系统升级
- 替代现有虚拟机（VM）
- 作为共用（非持久） RDSH 或 VDI 环境的一部分的用户

Microsoft 产品针对远程用户配置文件（包括以下技术）运行多项技术：
- 漫游用户配置文件（RUP）
- 用户配置文件磁盘（UPD）
- 企业状态漫游（ESR）

UPD 和 RUP 是远程桌面会话主机（RDSH）和虚拟硬盘（VHD）环境中用于用户配置文件的最广泛使用的技术。

### <a name="challenges-with-previous-user-profile-technologies"></a>以前的用户配置文件技术面临的挑战

用户配置文件的现有和旧版 Microsoft 解决方案附带了各种挑战。 以前的解决方案都不处理 RDSH 或 VDI 环境附带的所有用户配置文件需求。 例如，UPD 无法处理大型 OST 文件，并且 RUP 不会保留新式设置。

#### <a name="functionality"></a>功能

下表显示了以前的用户配置文件技术的优点和局限性。

| 技术 | 新式设置 | Win32 设置 | OS 设置 | 用户数据 | 在服务器 SKU 上受支持 | Azure 上的后端存储 | 本地后端存储 | 版本支持 | 后续登录时间 |说明|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **用户配置文件磁盘（UPD）** | 是 | 是 | 是 | 是 | 是 | 否 | 是 | Win 7 + | 是 | |
| **漫游用户配置文件（RUP），维护模式** | 否 | 是 | 是 | 是 | 是| 否 | 是 | Win 7 + | 否 | |
| **企业状态漫游（ESR）** | 是 | 否 | 是 | 否 | 请参阅说明 | 是 | 否 | Win 10 | 否 | 服务器 SKU 上的函数，但不支持用户界面 |
| **用户体验虚拟化 (UE-V)** | 是 | 是 | 是 | 否 | 是 | 否 | 是 | Win 7 + | 否 |  |
| **OneDrive 云文件** | 否 | 否 | 否 | 是 | 请参阅说明 | 请参阅说明  | 查看注释 | Win 10 RS3 | 否 | 未在服务器 SKU 上测试。 Azure 上的后端存储依赖于同步客户端。 本地上的后端存储需要同步客户端。 |

#### <a name="performance"></a>性能

UPD 需要[存储空间直通（S2D）](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)来满足性能要求。 UPD 使用服务器消息块（SMB）协议。 它将配置文件复制到记录用户的 VM。 UPD with S2D 是我们建议用于 Windows 虚拟桌面的解决方案。  

#### <a name="cost"></a>成本

虽然 S2D 群集实现了所需的性能，但企业客户的成本成本高昂，但对于中小型企业（SMB）客户，开销特别高。 对于此解决方案，企业需要支付存储空间，以及使用磁盘作为共享的 Vm 的成本。

#### <a name="administrative-overhead"></a>管理开销

S2D 群集要求在安全状态下对操作系统进行修补、更新和维护。 这些过程和设置 S2D 灾难恢复的复杂性使得 S2D 仅适用于具有专职 IT 人员的企业。

## <a name="fslogix-profile-containers"></a>FSLogix 配置文件容器

2018年11月19日， [Microsoft 获取了 FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)。 FSLogix 解决了许多配置文件容器挑战。 其中的关键是：

- **性能：**[FSLogix 配置文件容器](/fslogix/configure-profile-container-tutorial/)具有高性能，并解决过去已阻止缓存 exchange 模式的性能问题。
- **OneDrive：** 如果没有 FSLogix 配置文件容器，则在非持久性 RDSH 或 VDI 环境中不支持 OneDrive for Business。 [OneDrive for business 和 FSLogix 最佳实践](/fslogix/overview/)介绍了它们如何交互。 有关详细信息，请参阅[使用虚拟机上的同步客户端](/deployoffice/rds-onedrive-business-vdi/)。
- **其他文件夹：** FSLogix 提供扩展用户配置文件以包括更多文件夹的功能。

由于收购，Microsoft 开始将现有用户配置文件解决方案（例如 UPD）替换为 FSLogix 配置文件容器。

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure 文件与 Azure Active Directory 域服务的集成

FSLogix 配置文件容器的性能和功能将充分利用云。 2019年8月7日，Microsoft Azure[个文件通过 Azure Active Directory 域服务（AD DS）公布了 Azure 文件身份验证](../storage/files/storage-files-active-directory-overview.md)的公开上市。 通过解决成本和管理开销，具有 Azure AD DS 身份验证的 Azure 文件是 Windows 虚拟桌面服务中的用户配置文件的高级解决方案。

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 虚拟桌面的最佳实践

Windows 虚拟桌面提供对客户所使用的 Vm 大小、类型和计数的完全控制。 有关详细信息，请参阅[什么是 Windows 虚拟桌面？](overview.md)。

确保 Windows 虚拟桌面环境遵循最佳做法：

- Azure 文件存储帐户必须位于与会话主机 Vm 相同的区域中。
- Azure 文件权限应与[要求-配置文件容器](/fslogix/fslogix-storage-config-ht)中所述的权限匹配。
- 每个主机池都必须基于相同的主映像生成相同的类型和大小的 VM。
- 每个主机池 VM 必须位于同一个资源组中，以帮助管理、缩放和更新。
- 为了获得最佳性能，存储解决方案和 FSLogix 配置文件容器应位于相同的数据中心位置。
- 包含主映像的存储帐户必须位于要预配 Vm 的同一区域和订阅中。

## <a name="next-steps"></a>后续步骤

使用以下指南来设置 Windows 虚拟桌面环境。

- 若要开始构建桌面虚拟化解决方案，请参阅[在 Windows 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。
- 若要在 Windows 虚拟桌面租户中创建主机池，请参阅[使用 Azure Marketplace 创建主机池](create-host-pools-azure-marketplace.md)。
- 若要设置云中完全托管的文件共享，请参阅[设置 Azure 文件共享](/azure/storage/files/storage-files-active-directory-enable/)。
- 若要配置 FSLogix 配置文件容器，请参阅[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
- 若要将用户分配到主机池，请参阅[管理 Windows 虚拟桌面的应用组](manage-app-groups.md)。
- 若要通过 web 浏览器访问 Windows 虚拟桌面资源，请参阅[连接到 Windows 虚拟桌面](connect-web.md)。
