---
title: 存储 FSLogix 配置文件容器 Windows 虚拟桌面-Azure
description: 用于在 Azure 存储中存储 Windows 虚拟桌面 FSLogix 配置文件的选项。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4bb315991846b115070854b6e81caf16232fef6b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607175"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Windows 虚拟桌面中的 FSLogix 配置文件容器的存储选项

Azure 提供了多个存储解决方案，可用于存储 FSLogix 配置文件容器。 本文比较了 Azure 提供的用于 Windows 虚拟桌面 FSLogix 用户配置文件容器的存储解决方案。

Windows 虚拟桌面提供 FSLogix 的配置文件容器作为建议的用户配置文件解决方案。 FSLogix 设计用于在远程计算环境（如 Windows 虚拟桌面）中漫游配置文件。 登录时，此容器使用本机支持的虚拟硬盘（VHD）和 Hyper-v 虚拟硬盘（VHDX）动态连接到计算环境。 用户配置文件立即可用，并与本机用户配置文件完全相同。

下表比较了适用于 Windows 虚拟桌面 FSLogix profile 容器用户配置文件的 Azure 存储服务的存储解决方案。

## <a name="azure-platform-details"></a>Azure 平台详细信息

|功能|Azure 文件|Azure NetApp 文件|存储空间直通|
|--------|-----------|------------------|---------------------|
|用例|常规用途|从本地 NetApp 进行超高性能或迁移|跨平台|
|平台服务|是，Azure-本机解决方案|是，Azure-本机解决方案|不，自我管理|
|区域可用性|所有区域|[选择区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|所有区域|
|冗余|本地冗余/区域冗余/异地冗余|本地冗余|本地冗余/区域冗余/异地冗余|
|层和性能|Standard<br>Premium<br>每个共享最大为每个共享的最大 100k IOPS 约3毫秒，延迟|Standard<br>Premium<br>黑体<br>每卷 4.5 GBps 到320k （16K）的 IOPS 约1毫秒，延迟|标准 HDD：每个磁盘最多 500 IOPS<br>标准 SSD：最多为 4k IOPS 每个磁盘的限制<br>高级 SSD：每个磁盘的最大 IOPS 限制<br>建议存储空间直通的高级磁盘|
|容量|100每个共享 TiB|100 TiB 每个卷，每个订阅最多12.5 个 PiB|每个磁盘最多 32 TiB|
|必需的基础结构|最小共享大小 1 GiB|最小容量池 4 TiB，最小卷大小 100 GiB|Azure IaaS 上的两个 Vm （+ 云见证服务器）或至少三个 Vm，磁盘不含和费用|
|协议|SMB 2.1/3。 和 REST|NFSv3、NFSv 4.1 （预览版）、SMB 2.x/2。x|NFSv3，NFSv 4.1，SMB 3。1|

## <a name="azure-management-details"></a>Azure 管理详细信息

|功能|Azure 文件|Azure NetApp 文件|存储空间直通|
|--------|-----------|------------------|---------------------|
|访问|云、本地和混合（Azure 文件同步）|云，本地（通过 ExpressRoute）|云，本地|
|备份|Azure 备份快照集成|Azure NetApp 文件快照|Azure 备份快照集成|
|安全和符合性|[所有 Azure 支持的证书](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO 已完成|[所有 Azure 支持的证书](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory 集成|[本机 Active Directory 和 Azure Active Directory 域服务](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory 域服务和本机 Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|仅限本机 Active Directory 或 Azure Active Directory 域服务支持|

选择存储方法后，请查看[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)，了解有关定价计划的信息。

## <a name="next-steps"></a>后续步骤

若要了解有关 FSLogix 配置文件容器、用户配置文件磁盘和其他用户配置文件技术的详细信息，请参阅[FSLogix 配置文件容器和 Azure 文件](fslogix-containers-azure-files.md)中的表。

如果已准备好创建自己的 FSLogix 配置文件容器，请开始学习以下教程之一：

- [Windows 虚拟桌面中的 Azure 文件上的 FSLogix 配置文件容器入门](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](create-fslogix-profile-container.md)
- 使用 FSLogix 配置文件容器而不是用户配置文件磁盘时，在[Azure 中部署 UPD 存储的双节点存储空间直通横向扩展文件服务器](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)中的说明也适用

还可以从开始处开始，并在[Windows 虚拟桌面的 "创建租户" 中](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)设置自己的 Windows 虚拟桌面解决方案。
