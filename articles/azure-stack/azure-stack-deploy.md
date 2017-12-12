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
ms.date: 12/08/2017
ms.author: jeffgilb
ms.openlocfilehash: 2bfd9b2603575545fef1c26310a2eecd2c8968e4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
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


## <a name="next-steps"></a>后续步骤
[下载 Azure 堆栈开发工具包部署包](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[部署 Azure 堆栈开发工具包](azure-stack-run-powershell-script.md)

