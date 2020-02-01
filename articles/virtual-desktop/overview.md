---
title: 什么是 Windows 虚拟桌面？ - Azure
description: Windows 虚拟桌面的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 01/27/2020
ms.author: helohr
ms.openlocfilehash: 168a345427be47dc1c33f43be1af47daa8f638ef
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772786"
---
# <a name="what-is-windows-virtual-desktop"></a>什么是 Windows 虚拟桌面？ 

Windows 虚拟桌面是在云中运行的桌面和应用虚拟化服务。

下面是在 Azure 中运行 Windows 虚拟桌面时可以执行的操作：

* 设置多会话 Windows 10 部署，使整个 Windows 10 操作系统获得可伸缩性
* 虚拟化 Office 365 ProPlus，并将其优化为可在多用户虚拟方案中运行
* 为 Windows 7 虚拟桌面提供免费的扩展安全更新
* 将现有的远程桌面服务 (RDS) 和 Windows Server 桌面与应用迁移到任何计算机
* 虚拟化桌面和应用
* 使用统一的管理体验管理 Windows 10、Windows Server 和 Windows 7 桌面与应用

## <a name="introductory-video"></a>介绍性视频

通过此视频了解 Windows 虚拟桌面、它的独特之处及其新增功能：

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

有关 Windows 虚拟桌面的更多视频，请参阅[我们的播放列表](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)。

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

我们计划添加对以下操作系统的支持，因此请确保根据所要部署的桌面和应用，为用户提供[相应的许可证](https://azure.microsoft.com/pricing/details/virtual-desktop/)：

|OS|所需的许可证|
|---|---|
|Windows 10 Enterprise 多会话或 Windows 10 Enterprise|Microsoft 365 E3、E5、A3、A5、F1、Business<br>Windows E3、E5、A3、A5|
|Windows 7 Enterprise |Microsoft 365 E3、E5、A3、A5、F1、Business<br>Windows E3、E5、A3、A5|
|Windows Server 2012 R2、2016、2019|附带软件保障的 RDS 客户端访问许可证 (CAL)|

基础结构需要满足以下要求才能支持 Windows 虚拟桌面：

* 一个 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* 一个与 Azure Active Directory 同步的 Windows Server Active Directory。 可以通过以下方式之一对其进行配置：
  * Azure AD Connect（适用于混合组织）
  * Azure AD 域服务（适用于混合或云组织）
* 一个包含虚拟网络的 Azure 订阅，该虚拟网络包含或已连接到 Windows Server Active Directory
  
为 Windows 虚拟桌面创建的 Azure 虚拟机必须：

* [已加入标准域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison)，或[已加入混合 AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)。 虚拟机不能加入 Azure AD。
* 运行以下[受支持的 OS 映像](#supported-virtual-machine-os-images)之一。

>[!NOTE]
>如需 Azure 订阅，可以[注册一个月免费试用版](https://azure.microsoft.com/free/)。 如果使用免费试用版的 Azure，则应使用 Azure AD 域服务来使 Windows Server Active Directory 与 Azure Active Directory 保持同步。

为 Windows 虚拟桌面创建的 Azure 虚拟机必须能够访问以下 URL：

|地址|出站端口|目的|
|---|---|---|
|*.wvd.microsoft.com|TCP 端口 443|服务流量|
|\* .blob.core.windows.net|TCP 端口 443|代理、SXS 堆栈更新和代理流量|
|*.core.windows.net|TCP 端口 443|代理流量|
|*.servicebus.windows.net|TCP 端口 443|代理流量|
|prod.warmpath.msftcloudes.com|TCP 端口 443|代理流量|
|catalogartifact.azureedge.net|TCP 端口 443|Azure 市场|
|kms.core.windows.net|TCP 端口 1688|Windows 10 激活|

>[!IMPORTANT]
>若要可靠部署 Windows 虚拟桌面，必须打开这些 URL。 不支持阻止访问这些 URL，否则会影响服务功能。 这些 URL 仅对应于 Windows 虚拟桌面站点和资源，而不包括 Azure Active Directory 等其他服务的 URL。

>[!NOTE]
>Windows 虚拟桌面目前没有可以通过将其加入允许列表来允许网络流量的 IP 地址范围列表。 我们目前仅支持特定于允许列表操作的 URL。
>
>对于涉及服务流量的 URL，必须使用通配符 (*)。 如果不希望为代理相关的流量使用 *，下面提供了有关如何在不使用通配符的情况下查找 URL 的信息：
>
>1. 向 Windows 虚拟桌面主机池注册你的虚拟机。
>2. 打开“事件查看器”  ，导航到“Windows 日志” > “应用程序” > “WVD-Agent”并查找事件 ID 3702。   
>3. 将在事件 ID 3702 下找到的 URL 加入白名单。 事件 ID 3702 下的 URL 是特定于区域的。 你需要对要在其中部署虚拟机的每个区域的相关 URL 重复执行加入白名单过程。

Windows 虚拟桌面包括交付给用户的 Windows 桌面和应用，以及由 Microsoft 作为服务托管在 Azure 上的管理解决方案。 桌面和应用可以部署在任何 Azure 区域中的虚拟机 (VM) 上，这些 VM 的管理解决方案和数据将驻留在美国。 这可能会导致将数据传输到美国。

为获得最佳性能，请确保网络满足以下要求：

* 从客户端网络到部署主机池的 Azure 区域的往返 (RTT) 延迟应小于 150 毫秒。
* 当托管桌面和应用的 VM 连接到管理服务时，网络流量可能会流到国家/地区的边界以外。
* 为了优化网络性能，我们建议将会话主机的 VM 共置到管理服务所在的同一 Azure 区域。

## <a name="supported-remote-desktop-clients"></a>支持的远程桌面客户端

以下远程桌面客户端支持 Windows 虚拟桌面：

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android（预览）](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>受支持的虚拟机 OS 映像

Windows 虚拟桌面目前支持以下 x64 操作系统映像：

* Windows 10 Enterprise 多会话版本 1809 或更高版本
* Windows 10 Enterprise 版本 1809 或更高版本
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows 虚拟桌面不支持 x86（32 位）、Windows 10 企业版 N 或 Windows 10 企业版 KN 操作系统映像。 由于扇区大小限制，Windows 7 也不支持在托管 Azure 存储上托管的任何基于 VHD 或 VHDX 的配置文件解决方案。

可用的自动化和部署选项取决于所选的 OS 和版本，如下表所示： 

|操作系统|Azure 映像库|手动 VM 部署|Azure 资源管理器模板集成|在 Azure 市场中预配主机池|Windows 虚拟桌面代理更新|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 多会话版本 1903|是|是|是|是|自动|
|Windows 10 多会话版本 1809|是|是|否|否|自动|
|Windows 10 Enterprise 版本 1903|是|是|是|是|自动|
|Windows 10 Enterprise 版本 1809|是|是|否|否|自动|
|Windows 7 Enterprise|是|是|否|否|手动|
|Windows Server 2019|是|是|否|否|自动|
|Windows Server 2016|是|是|是|是|自动|
|Windows Server 2012 R2|是|是|否|否|自动|

## <a name="next-steps"></a>后续步骤

若要开始使用本服务，需要创建一个租户。 若要详细了解如何创建租户，请继续学习有关创建租户的教程。

> [!div class="nextstepaction"]
> [在 Windows 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)
