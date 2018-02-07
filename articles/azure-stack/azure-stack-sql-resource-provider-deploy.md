---
title: "使用 Azure 堆栈上的 SQL 数据库 |Microsoft 文档"
description: "了解如何部署 SQL 数据库作为 Azure 堆栈和快速步骤来部署 SQL Server 资源提供程序适配器上的服务。"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 SQL 数据库

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用 SQL Server 资源提供程序适配器来公开 SQL 数据库作为一项服务的[Azure 堆栈](azure-stack-poc.md)。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，可以创建你和你的用户：
- 对于云本机应用程序的数据库。
- 基于 SQL 的网站。
- 基于 SQL 的工作负荷。
你无需设置虚拟机 (VM) 在每次承载 SQL Server。

资源提供程序不支持的所有数据库管理功能[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)。 例如，弹性数据库池和自动向上和向下拨号数据库性能的功能将不可用。 但是，类似的资源提供程序执行支持创建、 读取、 更新和删除 (CRUD) 操作。 API 不与 SQL 数据库兼容。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构
资源提供程序包括三个组件：

- **SQL 资源提供程序适配器 VM**，这是运行的提供程序服务的 Windows 虚拟机。
- **资源提供程序本身**、 哪些流程预配请求和公开数据库资源。
- **承载 SQL Server 的服务器**，它提供的信息的数据库的容量调用宿主服务器。

你必须创建一个 （或多个） 的 SQL Server 的实例和/或提供对外部的 SQL Server 实例的访问。

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，注册你开发工具包，并下载可下载通过应用商店管理 Windows Server 2016 数据中心 Core 映像。 你必须使用 Windows Server 2016 Core 映像。 你还可以使用脚本创建[Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 （请务必选择核心选项）。 .NET 3.5 运行时不再是必需的。

2. 登录到可以访问特权终结点 VM 的主机。

    - 在 Azure 堆栈开发工具包安装中，登录到物理主机。

    - 在多节点系统中，主机必须是一个系统，可以访问特权终结点。
    
    >[!NOTE]
    > 正在运行该脚本的系统*必须*要安装的.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 系统。 安装否则将失败。 Azure 堆栈 SDK 主机都能满足此条件。


3. 下载二进制 SQL 资源提供程序。 然后运行自解压缩程序中，若要将内容提取到临时目录。

    >[!NOTE] 
    > 资源提供程序生成对应于 Azure 堆栈生成。 请务必下载正确版本的正在运行的 Azure 堆栈的二进制文件。

    | Azure 堆栈生成 | SQL 资源提供程序安装程序 |
    | --- | --- |
    |1.0.180102.3、 1.0.180103.2 或 1.0.180106.1 （多节点） | [SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP 版本 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP 版本 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. 从特权终结点中检索 Azure 堆栈根证书。 为 Azure 堆栈 SDK 中,，将创建一个自签名的证书作为此过程的一部分。 对于多节点，你必须提供适当的证书。

   若要提供自己的证书，放置在一个.pfx 文件**DependencyFilesLocalPath** ，如下所示：

    - 通配符证书\*.dbadapter。\<区域\>。\<外部 fqdn\>或使用 sqladapter.dbadapter 的公用名的单个站点证书。\<区域\>。\<外部 fqdn\>。

    - 此证书必须受信任。 即，信任链必须存在而无需中间证书。

    - 只有一个单个证书文件所在 DependencyFilesLocalPath。

    - 文件名称必须不包含任何特殊字符。


5. 打开**新**提升 （管理） 的 PowerShell 控制台和对您将文件解压缩 directory 的更改。 使用新窗口以避免可能不正确的已加载 PowerShell 模块从系统出现的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 运行 DeploySqlProvider.ps1 脚本，执行以下步骤：

    - 将证书和其他项目上载到 Azure 堆栈上的存储帐户。
    - 将发布库包，以便你可以部署通过库的 SQL 数据库。
    - 发布库包部署托管的服务器。
    - 使用在步骤 1 中创建，然后安装资源提供程序的 Windows Server 2016 映像部署 VM。
    - 注册映射到你的资源提供程序 VM 的本地 DNS 记录。
    - 注册资源提供程序与本地 Azure 资源管理器 （用户和管理员）。

> [!NOTE]
> 如果安装需要超过 90 分钟的时间，可能会失败。 如果失败，你看到失败消息，在屏幕上，在日志文件中，但部署的重试从失败的步骤。 不符合建议的内存和 vCPU 规范的系统可能不能部署 SQL 资源提供程序。
>

下面是可以从 PowerShell 提示符下运行的一个示例。 （请务必更改的帐户信息和根据需要的密码。）

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
你可以在命令行中指定这些参数。 如果你不希望这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 因为你用于部署 Azure 堆栈，请使用相同的凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 必须在此目录中放置您的证书.pfx 文件。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** | 重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 中删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>验证使用 Azure 堆栈门户部署

> [!NOTE]
>  安装脚本完成运行后，你需要刷新门户后，以查看管理边栏选项卡。


1. 服务管理员身份登录到管理门户。

2. 验证部署成功完成。 转到**资源组**。 然后选择**系统。\<位置\>.sqladapter**资源组。 验证所有四个部署成功完成。

      ![验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 SQL 资源提供程序适配器 （多节点仅，生成 1710年及更高版本）
每当更新 Azure 堆栈生成时，新的 SQL 资源提供程序适配器将被释放。 现有适配器可能会继续工作。 但是，我们建议更新到最新版本越早越好后更新 Azure 堆栈。 

更新过程是类似于前面所述的安装过程。 使用最新的资源提供程序代码中创建新的 VM。 此外，你将设置迁移到此新实例，包括数据库和托管服务器信息。 您也将迁移的必要的 DNS 记录。

UpdateSQLProvider.ps1 脚本使用前面所述的相同自变量。 你必须也提供此处所证书。

> [!NOTE]
> 此更新过程仅支持在多节点的系统上。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
你可以在命令行中指定这些参数。 如果你不希望这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 使用相同的凭据用于部署 Azure 堆栈。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 必须在此目录中放置您的证书.pfx 文件。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | （必需） |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** |重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 中删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |



## <a name="remove-the-sql-resource-provider-adapter"></a>删除 SQL 资源提供程序适配器

若要删除资源提供程序，请务必首先删除任何依赖关系。

1. 确保你有你下载的此版本的 SQL 资源提供程序适配器的原始部署包。

2. 从资源提供程序，必须删除所有用户数据库。 （删除用户数据库不会删除数据。）应由用户自己才能执行此任务。

3. 管理员必须从 SQL 资源提供程序适配器中删除宿主服务器。

4. 管理员必须删除任何引用 SQL 资源提供程序适配器的计划。

5. 管理员必须删除任何 Sku 和与 SQL 资源提供程序适配器关联的配额。

6. 重新运行部署脚本替换为以下元素：
    - -卸载参数
    - Azure 资源管理器终结点
    - The DirectoryTenantID
    - 服务管理员帐户的凭据


## <a name="next-steps"></a>后续步骤

[将宿主服务器添加](azure-stack-sql-resource-provider-hosting-servers.md)和[创建数据库](azure-stack-sql-resource-provider-databases.md)。

尝试使用其他[PaaS 服务](azure-stack-tools-paas-services.md)如[MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)和[应用程序服务资源提供程序](azure-stack-app-service-overview.md)。
