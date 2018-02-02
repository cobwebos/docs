---
title: "MySQL 数据库用作 Azure 堆栈上的 PaaS |Microsoft 文档"
description: "了解如何部署 MySQL 资源提供程序和提供作为 Azure 堆栈上的服务的 MySQL 数据库。"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: mabrigg
ms.openlocfilehash: db7daf61fa80854c17b58252d7d6cb30c329dfb1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 MySQL 数据库

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以部署 Azure 堆栈上的 MySQL 资源提供程序。 在部署资源提供程序后，你可以创建 MySQL 服务器和数据库通过 Azure 资源管理器部署模板。 你还可以作为一种服务提供 MySQL 数据库。 

MySQL 数据库，共有网站，支持多个网站平台。 例如，在部署资源提供程序后，你可以创建 WordPress 网站从 Web 应用程序平台作为服务 (PaaS) 外接程序用于 Azure 堆栈。

若要部署不能访问 Internet 的系统上的 MySQL 提供程序，请将文件复制[mysql 连接器 net 6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi)到本地共享。 当提示你输入它，然后提供该共享名称。 你必须安装的 Azure 和 Azure 堆栈 PowerShell 模块。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL Server 资源提供程序适配器体系结构

资源提供程序由三个组件组成：

- **MySQL 资源提供程序适配器 VM**，即 Windows 虚拟机运行的提供程序服务。

- **资源提供程序本身**、 哪些流程预配请求和公开数据库资源。

- **承载 MySQL Server 的服务器**，它提供的信息调用宿主服务器的数据库的容量。

此版本不再创建 MySQL 实例。 这意味着你需要自行创建和/或提供对外部的 SQL 实例的访问。 请访问[Azure 堆栈快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)有关示例模板，可以：
- 为你创建的 MySQL 服务器。
- 下载并部署从 Azure 应用商店创建 MySQL 服务器。

> [!NOTE]
> 从租户订阅，必须创建宿主的多节点 Azure 堆栈实现安装的服务器。 无法从默认提供程序订阅中创建它们。 从租户门户或使用相应登录的 PowerShell 会话，必须创建它们。 所有宿主服务器是应计费 Vm，并且必须具有合适的许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>必需的权限
系统帐户必须具有以下权限：

1.  数据库： 创建、 删除
2.  登录名： 创建、 设置、 删除、 授予、 撤消

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，注册你开发工具包，并下载可下载通过应用商店管理 Windows Server 2016 数据中心 Core 映像。 你必须使用 Windows Server 2016 Core 映像。 你还可以使用脚本创建[Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 （请务必选择核心选项。）.NET 3.5 运行时不再是必需的。


2. 登录到可以访问特权终结点 VM 的主机。

    - 在 Azure SDK 安装中，登录到物理主机。 
    - 在多节点系统中，主机必须是一个系统，可以访问特权终结点。
    
    >[!NOTE]
    > 系统在其运行该脚本*必须*要安装的.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 系统。 安装否则将失败。 Azure SDK 主机满足此条件。
    

3. 下载二进制 MySQL 资源提供程序。 然后运行自解压缩程序中，若要将内容提取到临时目录。

    >[!NOTE] 
    > 资源提供程序生成对应于 Azure 堆栈生成。 请务必下载正确版本的正在运行的 Azure 堆栈的二进制文件。

    | Azure 堆栈生成 | MySQL RP 安装程序 |
    | --- | --- |
    | 1.0.180102.3 或 1.0.180106.1 （多节点） | [MySQL RP 版本 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1.0.171122.1 | [MySQL RP 版本 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1.0.171028.1 | [MySQL RP 版本 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  从特权终结点中检索 Azure 堆栈根证书。 针对 Azure SDK 中，将创建一个自签名的证书作为此过程的一部分。 对于多节点，你必须提供适当的证书。

    如果你需要提供你自己的证书，放置在一个.pfx 文件**DependencyFilesLocalPath**满足以下条件：

    - 通配符证书\*.dbadapter。\<区域\>。\<外部 fqdn\>或使用 mysqladapter.dbadapter 的公用名的单个站点证书。\<区域\>。\<外部 fqdn\>。

    - 此证书必须受信任。 即，信任链必须存在而无需中间证书。

    - 只有一个单个证书文件所在 DependencyFilesLocalPath。
    
    - 文件名称必须不包含任何特殊字符。


5. 打开**新**提升 （管理） 的 PowerShell 控制台。 然后将更改为您将文件解压缩的目录。 使用新窗口以避免可能不正确的已加载 PowerShell 模块从系统出现的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 运行 `DeployMySqlProvider.ps1` 脚本。

脚本执行以下步骤：

* 下载 MySQL 连接器二进制文件 （这可以提供脱机）。
* 将证书和其他项目上载到 Azure 堆栈上的存储帐户。
* 将发布库包，以便你可以部署通过库的 SQL 数据库。
* 发布库包部署托管的服务器。
* 使用步骤 1 中创建的 Windows Server 2016 映像部署 VM。 它还会安装资源提供程序。
* 注册映射到你的资源提供程序 VM 的本地 DNS 记录。
* 注册资源提供程序与本地 Azure 资源管理器 （租户和管理员）。


可以：
- 在命令行上指定所需的参数。
- 运行不带任何参数，然后输入系统提示时。

下面是可以从 PowerShell 提示符下运行的一个示例。 请务必更改的帐户信息和根据需要的密码：


```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure SDK, the default is AzureStack, and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
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


### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
你可以在命令行中指定这些参数。 如果你不希望这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 使用相同的凭据用于部署 Azure 堆栈。 | _必需_ |
| **VMLocalCredential** | MySQL 资源提供程序 VM 的本地管理员帐户凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 包含本地共享路径[mysql 连接器 net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)。 如果你提供两个路径，该证书文件必须放置在此目录中。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** | 重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 中删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |
| **AcceptLicense** | 跳过的提示时接受 GPL 许可证。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |



具体取决于系统的性能和下载速度，安装可能需要最小为 20 分钟或长时间为几个小时。 如果**MySQLAdapter**边栏选项卡不可用，请刷新管理门户。

> [!NOTE]
> 如果安装需要超过 90 分钟的时间，可能会失败。 如果是这样，你会看到失败消息，在屏幕上，在日志文件中。 从失败的步骤重试部署。 不符合建议的内存和核心规范的系统可能不能部署 MySQL RP。



## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure 堆栈门户验证部署

> [!NOTE]
>  安装脚本完成运行后，你需要刷新门户后，以查看管理边栏选项卡。


1. 服务管理员身份登录到管理门户。

2. 验证部署成功完成。 转到**资源组**，然后选择**系统。\<位置\>.mysqladapter**资源组。 验证所有四个部署成功完成。

      ![验证 MySQL RP 的部署](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>提供通过连接到 MySQL 宿主服务器的容量

1. 登录到 Azure 堆栈门户为服务管理员。

2. 选择**管理资源** > **MySQL 宿主服务器** > **+ 添加**。

    上**MySQL 宿主服务器**边栏选项卡，可以将 MySQL Server 资源提供程序连接到实际的 MySQL Server 实例作为资源提供程序的后端。

    ![宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. 提供您的 MySQL Server 实例的连接详细信息。 请确保提供的完全限定的域名 (FQDN) 或有效的 IPv4 地址，并不是短 VM 名称。 此安装不再提供的默认 MySQL 实例。 提供的大小可帮助管理数据库容量资源提供程序。 它应接近数据库服务器的物理容量。

    > [!NOTE]
    >可以通过租户和管理 Azure 资源管理器访问 MySQL 实例，可以放置在控制之下的资源提供程序。 MySQL 实例*必须*分配以独占方式与资源提供程序。

4. 添加服务器时，你必须为他们分配一种新的或现有的 SKU，以允许服务产品的不同之处。
  例如，你可以具有企业实例提供：
    - 数据库容量
    - 自动备份
    - 预留各部门的高性能服务器
 

SKU 名称应反映的属性，以便租户可以适当地将其数据库。 在 SKU 中的所有宿主服务器应具有相同的功能。

![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> Sku 可以花费一个小时的时间无法在门户中显示。 无法创建一个数据库，直到创建 SKU。


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>通过创建你的第一个 MySQL 数据库来测试你的部署


1. 登录到 Azure 堆栈门户为服务管理员。

2. 选择**+ 新** > **数据 + 存储** > **MySQL 数据库**。

3. 提供数据库详细信息。

    ![创建测试 MySQL 数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 选择一种的 SKU。

    ![选择一种的 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. 创建一个登录名设置。 你可以重用现有的登录名设置或创建一个新。 此设置包含的用户名称和数据库的密码。

    ![创建新的数据库登录名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    该连接字符串包含实际的数据库服务器名称。 从门户复制它。

    ![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> 用户名称的长度不能超过在 MySQL 5.7 32 个字符。 在早期版本中，它不能超过 16 个字符。


## <a name="add-capacity"></a>添加容量

通过在 Azure 堆栈门户中添加其他 MySQL 服务器添加容量。 其他服务器可以添加到一种新的或现有的 SKU。 请确保服务器特征都相同。


## <a name="make-mysql-databases-available-to-tenants"></a>使 MySQL 数据库可供租户使用
创建计划，并提供可使 MySQL 数据库供租户可用。 例如，添加 Microsoft.MySqlAdapter 服务、 添加配额，依次类推。

![创建计划，并提供要包括数据库](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>更新管理密码
可以通过第一个更改它的 MySQL server 实例上修改密码。 选择**管理资源** > **MySQL 宿主服务器**。 然后选择的宿主服务器。 在**设置**面板中，选择**密码**。

![更新管理员密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 MySQL 资源提供程序适配器 （多节点仅，生成 1710年及更高版本）
每当更新 Azure 堆栈生成时，新的 MySQL 资源提供程序适配器将被释放。 现有适配器可能会继续工作。 但是，我们建议更新到最新版本越早越好后更新 Azure 堆栈。 

更新过程是类似于前面所述的安装过程。 使用最新的资源提供程序代码中创建新的 VM。 然后你将设置迁移到此新实例，包括数据库和托管服务器信息。 您也将迁移的必要的 DNS 记录。

UpdateMySQLProvider.ps1 脚本使用前面所述的相同自变量。 也提供此处的证书。

> [!NOTE]
> 在多节点的系统上仅支持更新。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure SDK, the default is AzureStack and the default prefix is AzS.
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

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 parameters
你可以在命令行中指定这些参数。 如果不这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 因为你用于部署 Azure 堆栈，请使用相同的凭据。 | _必需_ |
| **VMLocalCredential** |SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | IP 地址或特权终结点的 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 必须在此目录中放置您的证书.pfx 文件。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** | 重试，以秒为单位之间的超时间隔。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源 （请参阅以下说明）。 | 否 |
| **DebugMode** | 阻止失败的自动清理。 | 否 |
| **AcceptLicense** | 跳过的提示时接受 GPL 许可证。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |

## <a name="remove-the-mysql-resource-provider-adapter"></a>删除 MySQL 资源提供程序适配器

若要删除资源提供程序，请务必首先删除任何依赖关系。

1. 确保你有你下载的此版本的资源提供程序的原始部署包。

2. 从资源提供程序，必须删除所有租户数据库。 （删除租户数据库不会删除数据。）通过租户本身，应执行此任务。

3. 从命名空间中，租户必须取消注册。

4. 管理员必须从 MySQL 适配器中删除宿主服务器。

5. 管理员必须删除任何引用 MySQL 适配器的计划。

6. 管理员必须删除任何与 MySQL 适配器关联的配额。

7. 重新运行部署脚本替换为以下元素：
    - -卸载参数
    - Azure 资源管理器终结点
    - The DirectoryTenantID
    - 服务管理员帐户的凭据
