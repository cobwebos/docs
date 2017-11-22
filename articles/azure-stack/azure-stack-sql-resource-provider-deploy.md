---
title: "使用 Azure 堆栈上的 SQL 数据库 |Microsoft 文档"
description: "了解如何部署 SQL 数据库作为 Azure 堆栈和快速的步骤，以部署 SQL Server 资源提供程序适配器服务"
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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 31ffd31b5d540617c4a7a1224e6cf0ee656c9678
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 SQL 数据库

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用 SQL Server 资源提供程序适配器来公开 SQL 数据库作为一项服务的[Azure 堆栈](azure-stack-poc.md)。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，可以创建你和你的用户：
- 对于云本机应用程序的数据库
- 基于 SQL 的网站
- 基于 SQL 你的工作负荷无需设置虚拟机 (VM) 在每次承载 SQL Server。

资源提供程序不支持的所有数据库管理功能[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)。 例如，弹性数据库池和自动拨号高和调低的数据库性能的功能将不可用。 但是，类似的资源提供程序执行支持创建、 读取、 更新和删除 (CRUD) 操作。 API 不符合 SQL DB。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构
资源提供程序由三个组件组成：

- **SQL 资源提供程序适配器 VM**，后者是运行的提供程序服务的 Windows 虚拟机。
- **资源提供程序本身**、 哪些流程预配请求和公开数据库资源。
- **承载 SQL Server 的服务器**，它提供的信息对于数据库，容量称为托管服务器。

你必须创建一个 （或多个） SQL server 和/或提供对外部的 SQL 实例的访问。

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，注册你开发工具包，并下载可下载通过应用商店管理 Windows Server 2016 数据中心 Core 映像。 你必须使用 Windows Server 2016 Core 映像。 你还可以使用脚本创建[Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)-请务必选择核心选项。 .NET 3.5 运行时不再是必需的。

2. 登录到可以访问特权的终结点 VM 的主机。

    a. 在 Azure 堆栈开发工具包 (ASDK) 安装中，登录到物理主机。

    b. 在多节点系统中，主机必须是一个系统，可以访问特权终结点。

3. [下载 SQL 资源提供程序二进制文件文件](https://aka.ms/azurestacksqlrp)和执行自动解压缩程序中，若要将内容提取到临时目录。

    > [!NOTE]
    > 如果你在 Azure 堆栈上运行生成 20170928.3 或更早版本，[下载此版本](https://aka.ms/azurestacksqlrp1709)。

4. 从特权终结点中检索 Azure 堆栈根证书。 为 ASDK 中,，将创建一个自签名的证书作为此过程的一部分。 对于多节点，你必须提供适当的证书。

    如果你需要提供你自己的证书，你需要以下证书：

    通配符证书\*.dbadapter。\<区域\>。\<外部 fqdn\>。 此证书必须受信任，如将由证书颁发机构颁发。 即，信任链必须存在而无需中间证书。 可使用在安装过程中使用显式的 VM 名称 [sqladapter] 的单个站点证书。


5. 打开**新**提升 （管理） 的 PowerShell 控制台和对您将文件解压缩 directory 的更改。 使用新窗口以避免可能不正确已加载的 PowerShell 模块从系统出现的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 使用下面列出的参数运行 DeploySqlProvider.ps1 脚本。

脚本执行以下步骤：

- 将证书和其他项目上载到 Azure 堆栈上的存储帐户。
- 发布库包，以便你可以部署通过库的 SQL 数据库。
- 发布库包部署托管服务器
- 使用在步骤 1 中创建的 Windows Server 2016 映像部署 VM 并安装资源提供程序。
- 注册映射到你的资源提供程序 VM 的本地 DNS 记录。
- 注册资源提供程序与本地 Azure 资源管理器 （用户和管理员）。

> [!NOTE]
> 如果安装需要超过 90 分钟，则可能会失败，用户看到失败消息在屏幕上，在日志文件中，但部署的重试从失败的步骤。 不符合建议的内存和 vCPU 规范的系统可能不能部署 SQL RP。
>

下面是你可以从 PowerShell 运行的示例提示 （但根据需要更改的帐户信息和密码）：

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
你可以在命令行中指定这些参数。 如果你不希望这样做，或任何参数验证失败，系统会提示你提供所需的。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | （必需） |
| **AzCredential** | 提供 Azure 堆栈服务管理员帐户的凭据。 使用相同的凭据用于部署 Azure 堆栈）。 | （必需） |
| **VMLocalCredential** | 定义 SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | （必需） |
| **PrivilegedEndpoint** | 提供的 IP 地址或特权终结点的 DNS 名称。 |  （必需） |
| **DependencyFilesLocalPath** | 您的证书 PFX 文件必须放置在此目录中。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码 | （必需） |
| **MaxRetryCount** | 定义你想要重试每个操作，如果有多少次失败。| 2 |
| **RetryDuration** | 定义重试，以秒为单位之间的超时值。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源 （请参阅下面的注释） | 否 |
| **DebugMode** | 阻止失败的自动清理 | 否 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>验证使用 Azure 堆栈门户部署

> [!NOTE]
>  安装脚本完成后，你将需要刷新门户后，以查看管理边栏选项卡。


1. 服务管理员身份登录到管理门户。

2. 验证部署成功完成。 浏览**资源组** &gt;，单击**系统。\<位置\>.sqladapter**资源组，并验证所有四个部署成功。

      ![验证部署的 SQL RP](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="remove-the-sql-resource-provider-adapter"></a>删除 SQL 资源提供程序适配器

若要删除资源提供程序，请务必首先删除任何依赖关系。

1. 确保你有你下载的此版本的 SQL 资源提供程序适配器的原始部署包。

2. 从资源提供程序 （这不会删除数据），必须删除所有用户数据库。 这应由用户自己执行。

3. 管理员必须从 SQL 资源提供程序适配器中删除宿主服务器

4. 管理员必须删除任何引用 SQL 资源提供程序适配器的计划。

5. 管理员必须删除任何 Sku 和关联到 SQL 资源提供程序适配器的配额。

6. 重新运行的部署脚本的卸载参数、 Azure 资源管理器终结点、 DirectoryTenantID 和服务管理员帐户凭据。


## <a name="next-steps"></a>后续步骤

[添加承载服务器](azure-stack-sql-resource-provider-hosting-servers.md)和[创建数据库](azure-stack-sql-resource-provider-databases.md)。

尝试使用其他[PaaS 服务](azure-stack-tools-paas-services.md)如[MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)和[应用程序服务资源提供程序](azure-stack-app-service-overview.md)。
