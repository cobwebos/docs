---
title: Azure 迁移设备体系结构
description: 提供服务器评估和迁移中使用的 Azure 迁移设备的概述。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 25dc530199cde3408ce3bd6641aeb9bb8595465d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337592"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure 迁移设备体系结构

本文介绍 Azure 迁移设备体系结构和过程。 Azure 迁移设备是一种轻量级设备，部署在内部，用于发现要评估的 VM 和物理服务器，以便迁移到 Azure。 

## <a name="deployment-scenarios"></a>部署方案

Azure 迁移设备用于以下方案。

**方案** | **工具** | **用于** 
--- | --- | ---
**VMware VM 评估** | Azure 迁移：服务器评估 | 发现 VMware VM。<br/><br/> 发现计算机应用和依赖项。<br/><br/> 收集计算机元数据和性能元数据并发送到 Azure。
**VMware VM 迁移（无代理）** | Azure 迁移：服务器迁移 | 发现 VMware VM<br/><br/>  使用[无代理迁移](server-migrate-overview.md)复制 VMware VM。
**超 V VM 评估** | Azure 迁移：服务器评估 | 发现超虚拟机。<br/><br/> 收集计算机元数据和性能元数据并发送到 Azure。
**物理机器** |  Azure 迁移：服务器评估 |  发现物理服务器。<br/><br/> 收集计算机元数据和性能元数据并发送到 Azure。

## <a name="appliance-components"></a>设备组件

本产品具有多个组件。

- **管理应用**：这是一个 Web 应用程序，用于在设备部署期间用户输入。 用于评估计算机以迁移到 Azure 时使用。
- **发现代理**：代理收集计算机配置数据。 用于评估计算机以迁移到 Azure 时使用。 
- **评估代理**：代理收集性能数据。 用于评估计算机以迁移到 Azure 时使用。
- **DRA 代理**：协调 VM 复制，并协调复制的计算机和 Azure 之间的通信。 仅在使用无代理迁移将 VMware VM 复制到 Azure 时使用。
- **网关**：向 Azure 发送复制的数据。 仅在使用无代理迁移将 VMware VM 复制到 Azure 时使用。
- **自动更新服务**：更新设备组件（每 24 小时运行一次）。



## <a name="appliance-deployment"></a>设备部署

- 可以使用模板（仅限 Hyper-V 或 VMware）或 PowerShell 脚本安装程序设置 Azure 迁移设备。 [详细了解](deploy-appliance.md#deployment-options)这些选项。 
- 设备支持要求和部署先决条件汇总到[设备支持矩阵](migrate-appliance.md)中。


## <a name="appliance-registration"></a>设备注册

在设备设置期间，您将设备注册到 Azure 迁移。 在设置和注册过程中，将发生表中汇总的操作。

**操作** | **详细信息** | **权限**
--- | --- | ---
**注册源提供程序** | 这些资源提供程序在设备设置期间您选择的订阅中注册：Microsoft.OffAzure、微软.移民和微软.KeyVault。<br/><br/> 通过注册资源提供程序来配置订阅，以供资源提供程序使用。 | 需要订阅的“参与者”或“所有者”角色才能注册资源提供程序。
**创建 Azure AD 应用通信** | Azure 迁移创建 Azure 活动目录 （Azure AD） 应用，用于在设备上运行的代理与其在 Azure 上运行的相应服务之间的通信（身份验证和授权）。<br/><br/> 此应用没有对 Azure 资源管理器调用或 RBAC 访问任何资源的权限。 | 创建应用需要 Azure 迁移[的这些权限](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)。
**创建 Azure AD 应用 - 密钥保管库** | 此应用仅用于将 VMware VM 不代理迁移到 Azure。<br/><br/> 它仅用于访问用户订阅中为无代理迁移创建的密钥保管库。<br/><br/> 当从设备启动发现时，它在 Azure 密钥保管库（在客户的租户中创建）上具有 RBAC 访问权限。 | 创建应用需要 Azure 迁移[的这些权限](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)。



## <a name="collected-data"></a>收集的数据

客户端为所有部署方案收集的数据在[设备支持矩阵](migrate-appliance.md)中完全捕获。

## <a name="discovery-and-collection-process"></a>发现和收集过程

![体系结构](./media/migrate-appliance-architecture/architecture.png)

设备使用以下过程与 vCenter 服务器和 Hyper-V 主机/群集进行通信。

1. **开始发现**：
    - 在 Hyper-V 设备上启动发现时，它将与 WinRM 端口 5985 （HTTP） 和 5986 （HTTPS） 上的 Hyper-V 主机进行通信。
    - 当您开始在 VMware 设备上发现时，默认情况下它与 TCP 端口 443 上的 vCenter 服务器通信。 如果 vCenter 服务器侦听其他端口，则可以在设备 Web 应用中对其进行配置。
2. **收集元数据和性能数据**：
    - 设备使用通用信息模型 （CIM） 会话从端口 5985 和 5986 上的 Hyper-V 主机收集 Hyper-V VM 数据。
    - 默认情况下，设备与端口 443 通信，以便从 vCenter 服务器收集 VMware VM 数据。
3. **发送数据**：设备通过 SSL 端口 443 将收集的数据发送到 Azure 迁移服务器评估和 Azure 迁移服务器迁移。 设备可以通过 Internet 连接到 Azure，也可以将 ExpressRoute 用于公共/Microsoft 对等互连。
    - 对于性能数据，设备收集实时利用率数据。
        - VMware 每 20 秒收集一次性能数据，Hyper-V 每 30 秒收集一次，每个性能指标。
        - 收集的数据将汇总，以创建单个数据点 10 分钟。
        - 峰值利用率值从所有 20/30 秒的数据点中选择，并发送到 Azure 进行评估计算。
        - 根据评估属性（50/90th/95th/99）中指定的百分位值，按升序对 10 分钟点进行排序，并使用适当的百分位数值计算评估
    - 对于服务器迁移，设备开始收集 VM 数据，并将其复制到 Azure。
4. **评估和迁移**：您现在可以使用 Azure 迁移服务器评估从设备收集的元数据创建评估。 此外，您还可以使用 Azure 迁移服务器迁移开始迁移 VMware VM 来协调无代理 VM 复制。





## <a name="appliance-upgrades"></a>设备升级

随着在设备上运行的 Azure 迁移代理更新，设备将升级。 这是自动发生的，因为默认情况下在设备上启用了自动更新。 您可以更改此默认设置以手动更新代理。

通过将HKEY_LOCAL_MACHINE_SOFTWARE_Microsoft_AzureAppliance"自动更新"键设置为 0 （DWORD））来关闭注册表中的自动更新。 如果您决定使用手动更新，请务必同时更新设备上的所有代理，使用设备上每个过期代理的 **"更新**"按钮。
 

## <a name="next-steps"></a>后续步骤

[查看](migrate-appliance.md)设备支持矩阵。

