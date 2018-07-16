---
title: 在 Azure Stack 中使用 MySQL 数据库 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署 MySQL 数据库即服务，并通过便捷的步骤部署 MySQL Server 资源提供程序适配器。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 645fa89bede1311215f1d67c64a2388e4de5c1b1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044877"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>部署 Azure Stack 上的 MySQL 资源提供程序

使用 MySQL Server 资源提供程序作为 Azure Stack 服务公开 MySQL 数据库。 MySQL 资源提供程序作为服务运行 Windows Server 2016 Server Core 虚拟机 (VM) 上。

## <a name="prerequisites"></a>必备组件

有几个系统必备组件，需要准备就绪后，才能部署 Azure Stack MySQL 资源提供程序。 若要满足这些要求，完成这篇文章中可以访问特权终结点 VM 的计算机上的步骤。

* 向 Azure [注册 Azure Stack](.\azure-stack-registration.md)（如果尚未执行此操作），以便可以下载 Azure 市场项。
* 您必须要运行此安装在系统上安装的 Azure 和 Azure Stack PowerShell 模块。 该系统必须是.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 映像。 请参阅[安装适用于 Azure Stack PowerShell](.\azure-stack-powershell-install.md)。
* 下载 **Windows Server 2016 Datacenter - Server Core** 映像，将所需的 Windows Server 核心 VM 添加到 Azure Stack 市场。

* 下载 MySQL 资源提供程序二进制文件，然后运行自解压程序，将内容提取到临时目录。

  >[!NOTE]
  >若要部署 MySQL 提供程序不具备 Internet 访问的系统上，复制[mysql-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)到本地路径。 提供使用路径名称**DependencyFilesLocalPath**参数。

* 资源提供程序有一个相应的 Azure Stack 最低内部版本。 请确保下载正确版本的正在运行的 Azure Stack 的二进制文件：

    | Azure Stack 版本 | MySQL RP 版本|
    | --- | --- |
    | 版本 1804 (1.0.180513.1)|[MySQL RP 版本 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | 版本 1802 (1.0.180302.1) | [MySQL RP 版本 1.1.18.0](https://aka.ms/azurestackmysqlrp1802)|
    |     |     |

- 请确保满足数据中心集成先决条件：

    |先决条件|参考|
    |-----|-----|
    |条件性 DNS 转发会正确设置。|[Azure Stack 数据中心集成-DNS](azure-stack-integrate-dns.md)|
    |资源提供程序的入站的端口处于打开状态。|[Azure Stack 数据中心集成-发布终结点](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI 证书使用者和 SAN 都正确设置。|[Azure Stack 部署必需 PKI 先决条件](azure-stack-pki-certs.md#mandatory-certificates)<br>[Azure Stack 部署 PaaS 证书先决条件](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>证书

_仅适用于集成的系统安装_。 必须提供 [Azure Stack 部署 PKI 要求](.\azure-stack-pki-certs.md#optional-paas-certificates)中的“可选 PaaS 证书”部分所述的 SQL PaaS PKI 证书。 将 .pfx 文件放在 **DependencyFilesLocalPath** 参数指定的位置。 对于 ASDK 系统不提供证书。

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

安装所有必备组件之后，运行**DeployMySqlProvider.ps1**脚本，以部署 MYSQL 资源提供程序。 DeployMySqlProvider.ps1 脚本提取作为你的 Azure Stack 版本下载 MySQL 资源提供程序二进制文件的一部分。

若要部署 MySQL 资源提供程序，打开新的提升权限的 PowerShell 窗口 (而不是 PowerShell ISE) 并将更改为提取的 MySQL 资源提供程序二进制文件的目录。 我们建议使用新的 PowerShell 窗口，以避免已加载的 PowerShell 模块造成问题。

运行**DeployMySqlProvider.ps1**脚本，完成以下任务：

* 将证书和其他项目上传到 Azure Stack 上的存储帐户。
* 发布库包，以便您可以将部署使用库中的 MySQL 数据库。
* 发布用于部署宿主服务器的库包。
* 部署使用 Windows Server 2016 core 映像下载，然后再安装 MySQL 资源提供程序的 VM。
* 注册映射到资源提供程序 VM 的本地 DNS 记录。
* 注册资源提供程序与本地 Azure 资源管理器的操作员帐户。

> [!NOTE]
> MySQL 资源提供程序部署开始时，则**system.local.mysqladapter**创建资源组。 可能需要最多 75 分钟才能完成到此资源组所需的部署。

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 参数

可以指定这些参数从命令行。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | MySQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 对于集成系统，证书.pfx 文件必须放置此目录中。 有关断开连接的环境，下载[mysql-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)到此目录。 （可选） 可以复制一个 Windows 更新 MSU 包此处。 | _可选_(_必需_集成的系统或断开连接的环境) |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |
| **AcceptLicense** | 跳过接受 GPL 许可条款的提示。  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>部署 MySQL 资源提供程序使用自定义脚本

若要在部署资源提供程序时消除任何手动配置，可以自定义以下脚本。 更改 Azure Stack 部署所需的默认帐户信息和密码。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.3.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

资源提供程序安装脚本完成后，刷新浏览器，以确保能够看到最新的更新。

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署

1. 以服务管理员身份登录到管理门户。
2. 选择**资源组**
3. 选择**系统。\<位置\>.mysqladapter**资源组。
4. 在资源组概述摘要页上，应为没有部署失败。

## <a name="next-steps"></a>后续步骤

[添加宿主服务器](azure-stack-mysql-resource-provider-hosting-servers.md)
