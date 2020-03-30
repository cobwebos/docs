---
title: 存储 FSLogix 配置文件容器 Windows 虚拟桌面 - Azure
description: 用于在 Azure 存储上存储 Windows 虚拟桌面 FSLogix 配置文件的选项。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127517"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Windows 虚拟桌面中 FSLogix 配置文件容器的存储选项

Azure 提供了多个存储解决方案，可用于存储 FSLogix 配置文件容器。 本文比较 Azure 为 Windows 虚拟桌面 FSLogix 用户配置文件容器提供的存储解决方案。

Windows 虚拟桌面提供 FSLogix 配置文件容器作为推荐的用户配置文件解决方案。 FSLogix 设计用于在远程计算环境中（如 Windows 虚拟桌面）中漫游配置文件。 登录时，此容器使用本机支持的虚拟硬盘 （VHD） 和超 V 虚拟硬盘 （VHDX） 动态连接到计算环境。 用户配置文件立即可用，并且与本机用户配置文件完全一样显示在系统中。

下表比较了 Azure 存储为 Windows 虚拟桌面 FSLogix 配置文件容器用户配置文件提供的存储解决方案。

## <a name="azure-platform-details"></a>Azure 平台详细信息

|功能|Azure 文件|Azure NetApp 文件|存储空间直通|
|--------|-----------|------------------|---------------------|
|用例|常规用途|超高性能或从 NetApp 本地迁移|跨平台|
|平台服务|是，Azure 本机解决方案|是，Azure 本机解决方案|不，自我管理|
|区域可用性|所有区域|[选择区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|所有区域|
|冗余|本地冗余/区域冗余/异地冗余|本地冗余|本地冗余/区域冗余/异地冗余|
|层和性能|Standard<br>Premium<br>每股最多 100k IOPS，每股 5 GBps，延迟约 3 毫秒|Standard<br>Premium<br>超<br>高达 320k （16K） IOPS，每卷 4.5 GBps，延迟约 1 毫秒|标准硬盘：每个磁盘最多 500 个 IOPS 限制<br>标准 SSD：每个磁盘最多 4k IOPS 限制<br>高级 SSD：每个磁盘最多 20k IOPS 限制<br>我们建议存储空间直接的高级磁盘|
|容量|每股 100 TiB|每卷 100 TiB，每个订阅高达 12.5 PiB|每个磁盘最多 32 TiB|
|所需的基础设施|最小份额大小 1 GiB|最小容量池 4 TiB，最小体积大小 100 GiB|Azure IaaS 上的两个 VM（+ 云见证器）或至少三个 VM，没有磁盘的成本|
|协议|SMB 2.1/3。 和 REST|NFSv3， NFSv4.1 （预览）， SMB 3.x/2.x|NFSv3， NFSv4.1， SMB 3.1|

## <a name="azure-management-details"></a>Azure 管理详细信息

|功能|Azure 文件|Azure NetApp 文件|存储空间直通|
|--------|-----------|------------------|---------------------|
|访问|云、本地和混合（Azure 文件同步）|云，本地（通过快速路由）|云，本地|
|备份|Azure 备份快照集成|Azure NetApp 文件快照|Azure 备份快照集成|
|安全和符合性|[所有 Azure 支持的证书](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO 已完成|[所有 Azure 支持的证书](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory 集成|[本机活动目录和 Azure 活动目录域服务](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure 活动目录域服务和本机活动目录](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|仅支持本机活动目录或 Azure 活动目录域服务|

选择存储方法后，请查看[Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)，了解有关我们的定价计划的信息。

## <a name="next-steps"></a>后续步骤

要了解有关 FSLogix 配置文件容器、用户配置文件磁盘和其他用户配置文件技术的更多内容，请参阅[FSLogix 配置文件容器和 Azure 文件中](fslogix-containers-azure-files.md)的表。

如果您已准备好创建自己的 FSLogix 配置文件容器，请开始学习以下教程之一：

- [在 Windows 虚拟桌面中的 Azure 文件上使用 FSLogix 配置文件容器入门](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](create-fslogix-profile-container.md)
- 在 Azure[中部署用于 UPD 存储的双节点存储空间直接横向横向扩展文件服务器](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)中的说明也适用于使用 FSLogix 配置文件容器而不是用户配置文件磁盘时的说明

您也可以从一开始就在[Windows 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)中设置自己的 Windows 虚拟桌面解决方案。
