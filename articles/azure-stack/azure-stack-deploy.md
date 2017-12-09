---
title: "Azure 堆栈开发工具包部署先决条件 |Microsoft 文档"
description: "查看 Azure 堆栈开发工具包 （云操作员） 的环境和硬件要求。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: jeffgilb
ms.openlocfilehash: 8a0d23e14ef50034d5f9595cf154c3513a09c464
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Azure 堆栈部署先决条件

*适用范围： Azure 堆栈开发工具包*

在部署之前[Azure 堆栈开发工具包](azure-stack-poc.md)，请确保你的计算机满足以下要求：


## <a name="hardware"></a>硬件
| 组件 | 最小值 | 建议 |
| --- | --- | --- |
| 磁盘驱动器： 操作系统 |1 个 OS 磁盘的最小值为 200 GB 供系统分区 （SSD 或 HDD） |1 个 OS 磁盘的最小值为 200 GB 供系统分区 （SSD 或 HDD） |
| 磁盘驱动器： 常规开发工具包数据 * |4 个磁盘。 每个磁盘提供最少 140 GB 的容量 （SSD 或 HDD）。 将使用所有可用的磁盘。 |4 个磁盘。 每个磁盘提供 250 GB 的容量 （SSD 或 HDD） 的最小值。 将使用所有可用的磁盘。 |
| 计算： CPU |双套接字： 12 物理内核数 （总计） |双套接字： 16 物理内核数 （总计） |
| 计算： 内存 |96 GB 的 RAM |128 GB RAM （这是最小值，以支持 PaaS 资源提供程序）。|
| 计算： BIOS |HYPER-V 启用 （具有 SLAT 支持） |HYPER-V 启用 （具有 SLAT 支持） |
| 网络： NIC |Windows Server 2012 R2 认证所需的 NIC;没有所需的专用的功能 |Windows Server 2012 R2 认证所需的 NIC;没有所需的专用的功能 |
| 硬件徽标认证 |[针对 Windows Server 2012 R2 认证](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[针对 Windows Server 2012 R2 认证](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*你将需要更多数量如果你计划将添加的许多建议容量这样[应用商店项](azure-stack-download-azure-marketplace-item.md)从 Azure。

**数据磁盘驱动器配置：**所有数据驱动器都必须都是相同的类型 （所有 SAS、 所有 SATA 或所有 NVMe） 和容量。 如果使用 SAS 磁盘驱动器，则必须通过单个路径 （提供任何 MPIO，多路径支持） 附加磁盘驱动器。

**HBA 配置选项**

* （首选）简单的 HBA
* 必须在"传递"模式下配置 RAID HBA – 适配器
* 磁盘应配置为 RAID HBA – 单磁盘，RAID 0

**支持的总线和媒体类型组合**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (如果为未指定/未知的媒体类型\*)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* 使用 NVMe

\*如果不使用传递的功能的 RAID 控制器无法识别的媒体类型。 此类控制器会将 HDD 和 SSD 标记为未指定。 在这种情况下，固态硬盘将用作而不是缓存设备的持久存储。 因此，你可以部署这些各个 Ssd 上的开发工具包。

**示例 Hba**: LSI 9207 8i、 LSI 9300 8i 或在传递模式下的 LSI-9265-8i

提供了示例 OEM 配置。

## <a name="operating-system"></a>操作系统
|  | **要求** |
| --- | --- |
| **操作系统版本** |Windows Server 2012 R2 或更高版本。 操作系统版本不是很关键之前启动部署，如将主机计算机启动到 Azure 堆栈安装中包括的 VHD。 操作系统和所有必需的修补程序已集成到映像中。 不要使用任何键来激活开发工具包中使用任何 Windows Server 实例。 |

## <a name="deployment-requirements-check-tool"></a>部署要求检查工具
安装操作系统后，你可以使用[部署适用于 Azure 堆栈检查器](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)以确认你的硬件满足所有要求。

## <a name="account-requirements"></a>帐户要求
通常情况下，你的 internet 连接，你可以连接到 Microsoft Azure 部署开发工具包。 在这种情况下，你必须配置要部署的开发工具包的 Azure Active Directory (Azure AD) 帐户。

如果你的环境未连接到 internet，或者你不想要使用 Azure AD，你可以通过使用 Active Directory 联合身份验证服务 (AD FS) 来部署 Azure 堆栈。 开发工具包包括其自己的 AD FS 和 Active Directory 域服务实例。 如果使用此选项进行部署，你无需设置提前的帐户。

>[!NOTE]
如果使用 AD FS 选项进行部署，则必须重新部署 Azure 堆栈，以切换到 Azure AD。

### <a name="azure-active-directory-accounts"></a>Azure Active Directory 帐户
若要使用的 Azure AD 帐户中部署 Azure 堆栈，则必须在之前运行 PowerShell 脚本的部署准备 Azure AD 帐户。 此帐户将成为 Azure AD 租户的全局管理员。 它用于设置和委托应用程序和与 Azure Active Directory 和 Graph API 进行交互的所有 Azure 堆栈服务的服务主体。 它还用作默认提供程序订阅 （这可以在以后更改） 的所有者。 你可以使用此帐户登录到你的 Azure 堆栈系统管理员门户。

1. 创建为至少一个 Azure AD 目录管理员的 Azure AD 帐户。 如果你已经有一个，可以使用的。 否则，您可以创建一个免费在[http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (在中国，请访问<http://go.microsoft.com/fwlink/?LinkID=717821>相反)。 如果你于稍后计划[向 Azure 注册 Azure 堆栈](azure-stack-register.md)，还必须具有一个订阅，在这个新创建的帐户。
   
    保存的步骤 6 中使用这些凭据[部署开发工具包](azure-stack-run-powershell-script.md#deploy-the-development-kit)。 这*服务管理员*帐户可以配置和管理资源云、 用户帐户、 租户计划、 配额和定价。 在门户中，它们可以创建网站云、 虚拟机私有云、 创建计划和管理用户订阅。
2. [创建](azure-stack-add-new-user-aad.md)至少一个帐户，以便你可以登录到作为租户的开发工具包。
   
   | **Azure Active Directory 帐户** | **支持？** |
   | --- | --- |
   | 与有效的公共 Azure 订阅的工作或学校帐户 |是 |
   | 与有效的公共 Azure 订阅的 Microsoft 帐户 |是 |
   | 与有效中国 Azure 订阅的工作或学校帐户 |是 |
   | 与有效的美国政府 Azure 订阅的工作或学校帐户 |是 |

## <a name="network"></a>网络
### <a name="switch"></a>Switch
用于开发工具包机的交换机上的一个可用端口。  

开发工具包计算机支持连接到交换机访问端口或 trunk 端口。 没有特殊的功能需要在交换机上。 如果你正在使用 trunk 端口，或者如果你需要配置 VLAN ID，你必须提供作为部署参数的 VLAN ID。 你可以看到中的示例[的部署参数列表](azure-stack-run-powershell-script.md)。

### <a name="subnet"></a>子网
不能连接到以下子网开发工具包计算机：

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

这些子网将保留供开发工具包环境中的内部网络。

### <a name="ipv4ipv6"></a>IPv4/IPv6
支持仅 IPv4。 无法创建 IPv6 网络。

### <a name="dhcp"></a>DHCP
请确保 DHCP 服务器可用上没有 NIC 连接到网络。 如果 DHCP 不可用，你必须准备其他静态 IPv4 网络除了主机所使用的一个。 必须提供该 IP 地址和网关作为部署参数。 你可以看到中的示例[的部署参数列表](azure-stack-run-powershell-script.md)。

### <a name="internet-access"></a>Internet 访问权限
Azure 堆栈需要访问 Internet，直接或通过透明的代理。 Azure 堆栈不支持的 web 代理，以启用 Internet 访问权限的配置。 主机 IP 和新的 IP 分配给 MA BGPNAT01 （通过 DHCP 还是静态 IP） 必须能够访问 Internet。 在位于域 graph.windows.net 和 login.microsoftonline.com 域下使用端口 80 和 443。

## <a name="telemetry"></a>遥测

遥测有助于我们调整以后版本的 Azure 堆栈。 它可让我们快速地响应反馈，提供新功能，并提高质量。 Microsoft Azure 堆栈包括 Windows Server 2016 和 SQL Server 2014。 从默认设置更改，这些产品任一并且均有介绍通过 Microsoft 企业隐私声明。 Azure 堆栈还包含具有尚未经过修改以将遥测发送到 Microsoft 的开放源代码软件。 下面是 Azure 堆栈遥测数据的一些示例：

- 部署注册信息
- 则在打开和关闭警报
- 网络资源数

若要支持遥测数据流，必须在你的网络中打开端口 443 (HTTPS)。 客户端终结点是 https://vortex-win.data.microsoft.com。

如果你不想要为 Azure 堆栈提供遥测，你可以将其关闭，在开发工具包主机以及如下所述的基础结构虚拟机来使用。

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>关闭遥测开发工具包主机上 （可选）

>[!NOTE]
如果你想要关闭开发工具包宿主遥测，则必须在运行部署脚本之前执行操作。

之前[运行 asdk installer.ps1 脚本]()若要部署开发工具包宿主，引导 CloudBuilder.vhdx 并在提升的 PowerShell 窗口中运行以下脚本：
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

设置**AllowTelemetry**到 0 将关闭遥测 Windows 和 Azure 堆栈部署。 仅从操作系统的关键安全事件发送。 设置跨所有主机和基础结构 Vm，控制 Windows 遥测，并会重新应用到新的节点 Vm 发生横向扩展操作时。


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>关闭遥测基础结构的虚拟机上 （可选）

成功部署后，在开发工具包主机上 （作为 AzureStack\AzureStackAdmin 用户） 提升的 PowerShell 窗口中运行以下脚本：

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

若要配置 SQL Server 遥测，请参阅[如何配置 SQL Server 2016](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft)。

### <a name="usage-reporting"></a>使用情况报告

通过注册，Azure 堆栈也被配置为到 Azure 的正向使用情况信息。 从遥测独立控制使用情况报告。 你可以将使用情况报告时关闭[注册](azure-stack-register.md)使用 Github 上的脚本。 只需设置**$reportUsage**参数**$false**。

设置使用情况数据的格式详见[报表 Azure 堆栈使用情况数据迁移到 Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting)。 Azure 堆栈开发工具包用户不会实际收费。 此功能还包括开发工具包中，以便可以进行测试以查看使用情况报告的工作原理。 


## <a name="next-steps"></a>后续步骤
[下载 Azure 堆栈开发工具包部署包](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[部署 Azure 堆栈开发工具包](azure-stack-run-powershell-script.md)

