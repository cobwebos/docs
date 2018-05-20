---
title: 将 MySQL 数据库用作 Azure Stack 上的 PaaS | Microsoft Docs
description: 了解如何在 Azure Stack 上部署 MySQL 资源提供程序，并提供 MySQL 数据库即服务。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 4fb522e1a5a3c1adeaf5f46b8ccc3b9a852f4a88
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 MySQL 数据库

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以在 Azure Stack 上部署 MySQL 资源提供程序。 在部署资源提供程序之后，可以通过 Azure 资源管理器部署模板创建 MySQL 服务器和数据库。 还可以提供 MySQL 数据库即服务。 

网站上常用的 MySQL 数据库支持许多网站平台。 例如，在部署资源提供程序之后，可以从 Azure Stack 的 Web 应用平台即服务 (PaaS) 加载项创建 WordPress 网站。

若要在无法访问 Internet 的系统上部署 MySQL 提供程序，请将 [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) 文件复制到本地共享。 当系统提示输入共享名时，请提供该共享名。 必须安装 Azure 和 Azure Stack PowerShell 模块。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL 服务器资源提供程序适配器体系结构

资源提供程序由三个组件构成：

- **MySQL 资源提供程序适配器 VM**，即运行提供程序服务的 Windows 虚拟机。

- **资源提供程序本身**，处理预配请求并公开数据库资源。

- **托管 MySQL 服务器的服务器**，为称作宿主服务器的数据库提供容量。

此版本不再创建 MySQL 实例。 这意味着，必须自行创建它们和/或提供对外部 SQL 实例的访问权限。 请访问 [Azure Stack 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)，获取可以执行以下操作的示例模板：
- 创建 MySQL 服务器。
- 从 Azure Marketplace 下载并部署 MySQL 服务器。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集成系统上的宿主服务器， 而不能通过默认提供商订阅创建。 必须通过租户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器都是可计费的 VM，并且必须具有相应的许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>所需的特权
系统帐户必须拥有以下特权：

1.  数据库：创建、删除
2.  登录名：创建、设置、删除、授予、吊销

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，请注册开发工具包，并通过 Marketplace 管理下载 Windows Server 2016 Datacenter Core 映像。 必须使用 Windows Server 2016 Core 映像。 也可以使用脚本创建 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 （请务必选择“Core”选项）。


2. 登录到可访问特权终结点 VM 的主机。

    - 在 Azure SDK 安装中，登录到物理主机。 
    - 在多节点系统上，该主机必须是可访问特权终结点的系统。
    
    >[!NOTE]
    > 运行脚本的系统必须是装有最新版 .NET 运行时的 Windows 10 或 Windows Server 2016 系统。 否则安装会失败。 Azure Stack SDK 主机满足此条件。
    

3. 下载 MySQL 资源提供程序二进制文件。 然后运行自解压程序，将内容解压缩到临时目录。

    >[!NOTE] 
    > 资源提供程序有一个相应的 Azure Stack 最低内部版本。 请务必下载适用于运行中 Azure Stack 版本的正确二进制文件。

    | Azure Stack 内部版本 | MySQL RP 安装程序 |
    | --- | --- |
    | 1802：1.0.180302.1 | [MySQL RP 版本 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712：1.0.180102.3 或 1.0.180106.1（多节点） | [MySQL RP 版本 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711：1.0.171122.1 | [MySQL RP 版本 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710：1.0.171028.1 | [MySQL RP 版本 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  对于 Azure SDK，将在此过程中创建自签名证书。 对于多节点，必须提供相应的证书。

    如需提供自己的证书，请将满足以下条件的 .pfx 文件放在 **DependencyFilesLocalPath** 中：

    - \*.dbadapter.\<区域\>.\<外部 FQDN\> 的通配符证书，或采用 mysqladapter.dbadapter.\<区域\>.\<外部 FQDN\> 公用名的单个站点证书。

    - 此证书必须受信任。 也就是说，信任链必须存在，且不需要中间证书。

    - DependencyFilesLocalPath 中只存在单个证书文件。
    
    - 文件名不能包含任何特殊字符或空格。


5. 打开权限提升的（管理）**新** PowerShell 控制台。 然后切换到文件解压缩到的目录。 使用新窗口可以避免因系统上已加载的错误 PowerShell 模块可能造成的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 运行 `DeployMySqlProvider.ps1` 脚本。

该脚本执行以下步骤：

* 下载 MySQL 连接器二进制文件（可脱机提供）。
* 将证书和其他项目上传到 Azure Stack 上的存储帐户。
* 发布库包，以便通过库部署 SQL 数据库。
* 发布用于部署宿主服务器的库包。
* 使用步骤 1 中创建的 Windows Server 2016 映像部署 VM。 这也会安装资源提供程序。
* 注册映射到资源提供程序 VM 的本地 DNS 记录。
* 将资源提供程序注册到本地 Azure 资源管理器（租户和管理员）。


可以：
- 在命令行中指定所需的参数。
- 直接运行该命令而不指定任何参数，然后在出现提示时输入参数。

下面是可从 PowerShell 命令提示符运行的示例命令。 请务必根据需要更改帐户信息和密码：


```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

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

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 参数
可以在命令行中指定这些参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | MySQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 包含 [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) 的本地共享路径。 如果提供其中一个路径，则也必须将证书文件放在此目录中。 | _可选_（对于多节点部署是_必需_的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |
| **AcceptLicense** | 跳过接受 GPL 许可条款的提示。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |

>[!NOTE]
> SKU 最长可能需要在一小时后才显示在门户中。 在创建 SKU 之前，无法创建数据库。

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署

> [!NOTE]
>  在安装脚本完成运行之后，必须刷新门户才能看到管理边栏选项卡。


1. 以服务管理员身份登录到管理门户。

2. 验证部署是否成功。 转到“资源组”，然后选择“system.\<位置\>.mysqladapter”资源组。 验证所有四个部署是否成功。

      ![验证 MySQL RP 的部署](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>连接到 MySQL 宿主服务器以提供容量

1. 以服务管理员身份登录到 Azure Stack 门户。

2. 选择“管理资源” > “MySQL 宿主服务器” > “+添加”。

    在“MySQL 宿主服务器”边栏选项卡上，可将 MySQL 服务器资源提供程序连接到充当资源提供程序后端的实际 MySQL 服务器实例。

    ![宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. 提供 MySQL 服务器实例的连接详细信息。 请务必提供完全限定域名 (FQDN) 或有效的 IPv4 地址，而不是简短的 VM 名称。 此安装不再提供默认 MySQL 实例。 提供的大小可帮助资源提供程序管理数据库容量。 它应该接近数据库服务器的实际容量。

    > [!NOTE]
    >如果租户和管理 Azure 资源管理器可以访问 MySQL 实例，则资源提供程序可以控制此实例。 必须专门将 SQL 实例分配给资源提供程序。

4. 添加服务器时，必须将它们分配给新的或现有的 SKU，以区分服务产品。
  例如，可以分配一个企业实例来提供：
    - 数据库容量
    - 自动备份
    - 为各个部门保留高性能服务器
 

SKU 名称应反映属性，使租户能够适当地放置其数据库。 SKU 中的所有宿主服务器应有相同的功能。

![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)





## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>创建第一个 MySQL 数据库以测试部署


1. 以服务管理员身份登录到 Azure Stack 门户。

2. 选择“+ 新建” > “数据 + 存储” > “MySQL 数据库”。

3. 提供数据库详细信息。

    ![创建 MySQL 测试数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 选择 SKU。

    ![选择 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. 创建登录设置。 可以重复使用现有的登录设置，或创建新的登录设置。 此设置包含数据库的用户名和密码。

    ![新建数据库用户名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    连接字符串包含实际的数据库服务器名称。 请从门户复制连接字符串。

    ![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> MySQL 5.7 中的用户名长度不可超过 32 个字符。 在早期版本中不可超过 16 个字符。


## <a name="add-capacity"></a>添加容量

在 Azure Stack 门户中添加更多 MySQL 服务器以添加容量。 可将其他服务器添加到新的或现有的 SKU。 确保服务器特征相同。


## <a name="make-mysql-databases-available-to-tenants"></a>将 MySQL 数据库提供给租户使用
创建计划和产品/服务，使租户能够使用 MySQL 数据库。 例如，添加 Microsoft.MySqlAdapter 服务、增加配额，等等。

![创建计划和产品/服务以包含数据库](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>更新管理密码
若要修改密码，可以先在 MySQL 服务器实例上更改密码。 选择“管理资源” > “MySQL 宿主服务器”。 然后选择宿主服务器。 在“设置”面板中选择“密码”。

![更新管理密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 MySQL 资源提供程序适配器（仅限多节点，1710 和更高版本）
更新 Azure Stack 内部版本时，可能会发布新的 SQL 资源提供程序适配器。 虽然现有的适配器可以继续使用，但仍建议尽快更新到最新的内部版本。 

若要更新资源提供程序，请使用 *UpdateMySQLProvider.ps1* 脚本。 此过程类似于安装资源提供程序时所使用的过程，如本文[部署资源提供程序](#deploy-the-resource-provider)部分所述。 资源提供程序的下载包中提供此脚本。

*UpdateMySQLProvider.ps1* 脚本可使用最新的资源提供程序代码创建新的 VM，并可将设置从旧 VM 迁移到新 VM。 迁移的设置包括数据库和宿主服务器信息，以及必需的 DNS 记录。

此脚本需要使用的参数正是针对 DeployMySqlProvider.ps1 脚本进行描述的参数。 请同样在此处提供证书。 

下面是可从 PowerShell 提示符运行的 *UpdateMySQLProvider.ps1* 脚本的示例。 请务必根据需要更改帐户信息和密码： 

> [!NOTE]
> 此更新过程仅适用于集成系统。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

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

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 参数
可以在命令行中指定这些参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** |SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_（对于多节点部署是_必需_的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |
| **AcceptLicense** | 跳过接受 GPL 许可条款的提示。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>收集诊断日志
MySQL 资源提供程序是锁定的虚拟机。 如果必须从虚拟机收集日志，则会相应地提供 PowerShell Just Enough Administration (JEA) 终结点 _DBAdapterDiagnostics_。 可以通过此终结点使用两个命令：

* Get-AzsDBAdapterLog - 准备包含 RP 诊断日志的 zip 包并将其置于会话用户驱动器上。 此命令可以在不使用参数的情况下调用，将会收集过去四小时的日志。
* Remove-AzsDBAdapterLog - 清理资源提供程序 VM 上现有的日志包

在 RP 部署或更新期间会创建名为 _dbadapterdiag_ 的用户帐户，用于连接到诊断终结点以提取 RP 日志。 此帐户的密码就是在部署/更新期间为本地管理员帐户提供的密码。

若要使用这些命令，需创建一个连接到资源提供程序虚拟机的远程 PowerShell 会话，然后调用命令。 可以选择提供 FromDate 和 ToDate 参数。 如果不指定这其中的一个参数，或者两个参数都不指定，则 FromDate 为当前时间之前的四小时，ToDate 为当前时间。

以下示例脚本演示如何使用这些命令：

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>维护操作（集成系统）
MySQL 资源提供程序是锁定的虚拟机。 可以通过 PowerShell Just Enough Administration (JEA) 终结点 _DBAdapterMaintenance_ 更新资源提供程序虚拟机的安全性。

RP 的安装包随附了一个方便执行这些操作的脚本。


### <a name="update-the-virtual-machine-operating-system"></a>更新虚拟机操作系统
可以通过多种方式更新 Windows Server VM：
* 使用当前进行了修补的 Windows Server 2016 Core 映像安装最新的资源提供程序包
* 在安装或更新 RP 期间安装 Windows 更新包


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虚拟机 Windows Defender 定义

请按以下步骤更新 Defender 定义：

1. 从 [Windows Defender 定义](https://www.microsoft.com/en-us/wdsi/definitions)下载 Windows Defender 定义更新

    在该页的“Manually download and install the definitions”（手动下载和安装定义）下，下载“适用于 Windows 10 和 Windows 8.1 的 Windows Defender 防病毒”64 位文件。 
    
    直接链接：https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. 创建连接到 MySQL RP 适配器虚拟机的维护终结点的 PowerShell 会话
3. 使用维护终结点会话将定义更新文件复制到 DB 适配器虚拟机
4. 在维护 PowerShell 会话中，调用 _Update-DBAdapterWindowsDefenderDefinitions_ 命令
5. 安装以后，建议删除使用过的定义更新文件。 可以在维护会话中使用 _Remove-ItemOnUserDrive)_ 命令将其删除。


下面是一个用于更新 Defender 定义的示例脚本（请将虚拟机的地址或名称替换为实际值）：

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="remove-the-mysql-resource-provider-adapter"></a>删除 MySQL 资源提供程序适配器

若要删除资源提供程序，必须先删除所有依赖项。

1. 确保已保留针对此资源提供程序版本下载的原始部署包。

2. 必须从资源提供程序中删除所有租户数据库。 （删除租户数据库不会删除数据。）此任务应由租户自己执行。

3. 必须从命名空间中取消注册租户。

4. 管理员必须从 MySQL 适配器中删除宿主服务器。

5. 管理员必须删除引用 MySQL 适配器的所有计划。

6. 管理员必须删除与 MySQL 适配器关联的所有配额。

7. 使用以下元素重新运行部署脚本：
    - -Uninstall 参数
    - Azure 资源管理器终结点
    - DirectoryTenantID
    - 服务管理员帐户的凭据
