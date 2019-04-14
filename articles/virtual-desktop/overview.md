---
title: 什么是 Windows 虚拟桌面预览版？  - Azure
description: Windows 虚拟桌面预览版的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6385838064c408ccfa23dacbd5785f8e82f3cc8b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049421"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>什么是 Windows 虚拟桌面预览版？ 

Windows 虚拟桌面现已推出公共预览版，它是一个在云中运行的桌面和应用虚拟化服务。

下面是在 Azure 中运行 Windows 虚拟桌面时可以执行的操作：

* 设置多会话 Windows 10 部署，使整个 Windows 10 操作系统获得可伸缩性
* 虚拟化 Office 365 ProPlus，并将其优化为可在多用户虚拟方案中运行
* 为 Windows 7 虚拟桌面提供免费的扩展安全更新
* 将现有的远程桌面服务 (RDS) 和 Windows Server 桌面与应用迁移到任何计算机
* 虚拟化桌面和应用
* 使用统一的管理体验管理 Windows 10、Windows Server 和 Windows 7 桌面与应用

## <a name="key-capabilities"></a>关键功能

使用 Windows 虚拟桌面可以设置可缩放且灵活的环境：

* 在 Azure 订阅中创建完整的桌面虚拟化环境，而无需运行任何额外的网关服务器。
* 发布任意数目的主机池来适应各种工作负荷。
* 自带映像用于生产工作负荷，或者在 Azure 库中进行测试。
* 使用入池的多会话资源降低成本。 使用 Windows Server 上的 Windows 虚拟桌面和远程桌面会话主机 (RDSH) 角色专用的 Windows 10 Enterprise 多会话新功能，可以大幅降低虚拟机和操作系统 (OS) 的开销，同时仍可向用户提供相同的资源。
* 通过个人（持久性）桌面提供个人所有权。

可以部署和管理虚拟桌面：

* 使用 Windows 虚拟桌面 PowerShell 和 REST 接口配置主机池、创建应用组、分配用户和发布资源。
* 通过单个主机池发布完整桌面或单个远程应用、为不同的用户集创建单个应用组，甚至可将用户分配到多个应用组以减少映像数目。
* 管理环境时，可以使用内置的委托访问权限来分配角色，并可以收集诊断信息来了解各种配置错误或用户错误。
* 使用新的诊断服务来排查错误。
* 只能管理映像和虚拟机，而不能管理基础结构。 无需像在远程桌面服务中一样亲自管理远程桌面角色，而只需管理 Azure 订阅中的虚拟机。

此外，可以分配用户并将其连接到虚拟机：

* 分配后，用户可以启动任何 Windows 虚拟桌面客户端，以连接到其已发布的 Windows 桌面和应用程序。 可在任何设备中，通过设备上的本机应用程序或 Windows 虚拟桌面 HTML5 Web 客户端进行连接。
* 用户可以通过反向连接安全地与服务建立连接，因此，永远不需要将任何入站端口保持打开状态。

## <a name="requirements"></a>要求

设置 Windows 虚拟桌面并将用户成功连接到其 Windows 桌面和应用程序需要注意几个事项。

首先，请确保根据所要部署的桌面和应用，为用户提供[相应的许可证](https://azure.microsoft.com/pricing/details/virtual-desktop/)：

|操作系统|所需的许可证|
|---|---|
|Windows 10 Enterprise 多会话或 Windows 10 单会话|Microsoft 365 E3、E5、A3、A5、Business、F1<br>Windows E3、E5、A3、A5|
|Windows 7|Microsoft 365 E3、E5、A3、A5、Business、F1<br>Windows E3、E5、A3、A5|
|Windows Server 2012 R2、2016、2019|附带软件保障的 RDS 客户端访问许可证 (CAL)|

基础结构需要满足以下要求才能支持 Windows 虚拟桌面：

* 一个 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* 一个与 Azure Active Directory 同步的 Windows Server Active Directory。 可通过以下方式满足此要求：
  * Azure AD Connect
  * Azure AD 域服务
* 一个包含虚拟网络的 Azure 订阅，该虚拟网络包含或已连接到 Windows Server Active Directory
  
为 Windows 虚拟桌面创建的 Azure 虚拟机必须：

* [已加入标准域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison)，或[已加入混合 AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)。 虚拟机不能加入 Azure AD。
* 运行以下受支持的 OS 映像之一：
  * Windows 10 Enterprise 多会话
  * Windows Server 2016

>[!NOTE]
>如需 Azure 订阅，可以[注册一个月免费试用版](https://azure.microsoft.com/free/)。 如果使用免费试用版的 Azure，则应使用 Azure AD 域服务来使 Windows Server Active Directory 与 Azure Active Directory 保持同步。

Windows 虚拟桌面包括交付给用户的 Windows 桌面和应用，以及由 Microsoft 作为服务托管在 Azure 上的管理解决方案。 在公共预览期，桌面和应用可以部署在任何 Azure 区域中的虚拟机 (VM) 上，这些 VM 的管理解决方案和数据将驻留在美国（美国东部 2 区域）。 因此，在公共预览期测试服务时，数据可能会传输到美国。 开始推出正式版后，我们即会将管理解决方案和数据的本地化工作扩展到所有 Azure 区域。

为获得最佳性能，请确保网络满足以下要求：

* 从客户端网络到部署主机池的 Azure 区域的往返 (RTT) 延迟应小于 150 毫秒。
* 当托管桌面和应用的 VM 连接到管理服务时，网络流量可能会流到国家/地区的边界以外。
* 为了优化网络性能，我们建议将会话主机的 VM 共置到管理服务所在的同一 Azure 区域。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。 目前我们不会受理 Windows 虚拟桌面预览版的支持案例。

## <a name="next-steps"></a>后续步骤

若要开始使用本服务，需要创建一个租户。 若要详细了解如何创建租户，请继续学习有关创建租户的教程。

> [!div class="nextstepaction"]
> [在 Windows 虚拟桌面预览版中创建租户](tenant-setup-azure-active-directory.md)
