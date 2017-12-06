---
title: "MySQL 数据库用作 Azure 堆栈上的 PaaS |Microsoft 文档"
description: "了解如何部署 MySQL 资源提供程序和提供作为 Azure 堆栈上的服务的 MySQL 数据库"
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
ms.date: 11/29/2017
ms.author: JeffGo
ms.openlocfilehash: e1752bfe40fb53568b79e2b7eec56ca9f3139d4c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 MySQL 数据库

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以部署 Azure 堆栈上的 MySQL 资源提供程序。 在部署资源提供程序后，你可以创建 MySQL 服务器和数据库通过 Azure 资源管理器部署模板和作为服务提供 MySQL 数据库。 MySQL 数据库，共有网站，支持多个网站平台。 例如，部署资源提供程序之后, 你可以创建 WordPress 网站从 Azure Web Apps 平台作为服务 (PaaS) 外接程序用于 Azure 堆栈。

若要部署不能访问 internet 的系统上的 MySQL 提供程序，你可以将文件复制[mysql 连接器 net 6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi)到本地共享。 然后，提供出现提示时该共享名称。 你还必须安装的 Azure 和 Azure 堆栈 PowerShell 模块。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL Server 资源提供程序适配器体系结构

资源提供程序由三个组件组成：

- **MySQL 资源提供程序适配器 VM**，后者是运行的提供程序服务的 Windows 虚拟机。
- **资源提供程序本身**、 哪些流程预配请求和公开数据库资源。
- **承载 MySQL Server 的服务器**，它提供的信息对于数据库，容量称为托管服务器。

此版本不再创建的 MySQL 实例。 你必须创建它们和/或提供对外部的 SQL 实例的访问。 请访问[Azure 堆栈快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)有关示例模板，可以：
- 为你创建的 MySQL 服务器
- 下载并部署从应用商店创建 MySQL 服务器。

> [!NOTE]
> 从租户订阅，必须创建宿主服务器安装在多节点 Azure 堆栈上。 也不能从默认提供程序订阅中创建。 换而言之，则必须创建从租户门户或具有合适的登录名的 PowerShell 会话。 所有宿主服务器是应计费 Vm，并且必须具有合适的许可证。 服务管理员可以是该订阅的所有者。

### <a name="required-privileges"></a>必需的权限
系统帐户必须具有以下权限：

1.  数据库： 创建、 删除
2.  登录名： 创建、 设置、 删除、 授予、 撤消

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

1. 如果尚未这样做，注册你开发工具包，并下载可下载通过应用商店管理 Windows Server 2016 数据中心 Core 映像。 你必须使用 Windows Server 2016 Core 映像。 你还可以使用脚本创建[Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)-请务必选择核心选项。 .NET 3.5 运行时不再是必需的。


2. 登录到可以访问特权的终结点 VM 的主机。

    a. 在 Azure 堆栈开发工具包 (ASDK) 安装中，登录到物理主机。

    b. 在多节点系统中，主机必须是一个系统，可以访问特权终结点。

3. 下载二进制 MySQL 资源提供程序并执行自解压缩程序中，若要将内容提取到临时目录。

    >[!NOTE] 
    > 资源提供程序生成对应于 Azure 堆栈生成。 你必须下载正确版本的正在运行的 Azure 堆栈的二进制文件。

    | Azure 堆栈生成 | MySQL RP 安装程序 |
    | --- | --- |
    | 1.0.171122.1 | [MySQL RP 版本 1.1.10.0](https://aka.ms/azurestackmysqlrp) |
    | 1.0.171028.1 | [MySQL RP 版本 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |
    | 1.0.170928.3 | [MySQL RP 版本 1.1.3.0](https://aka.ms/azurestackmysqlrp1709) |

4.  从特权终结点中检索 Azure 堆栈根证书。 为 ASDK 中,，将创建一个自签名的证书作为此过程的一部分。 对于多节点，你必须提供适当的证书。

    如果你需要提供你自己的证书，你需要以下证书：

    通配符证书\*.dbadapter。\<区域\>。\<外部 fqdn\>。 此证书必须受信任，如将由证书颁发机构颁发。 即，信任链必须存在而无需中间证书。 可使用在安装过程中使用显式的 VM 名称 [mysqladapter] 的单个站点证书。


5. 打开**新**提升 （管理） 的 PowerShell 控制台和对您将文件解压缩 directory 的更改。 使用新窗口以避免可能不正确已加载的 PowerShell 模块从系统出现的问题。

6. [安装 Azure PowerShell 版本 1.2.11](azure-stack-powershell-install.md)。

7. 运行 DeploySqlProvider.ps1 脚本。

脚本执行以下步骤：

* 下载 MySQL 连接器二进制文件 （这可以提供脱机）。
* 将证书和其他项目上载到 Azure 堆栈上的存储帐户。
* 发布库包，以便你可以部署通过库的 SQL 数据库。
* 发布库包部署托管服务器
* 使用在步骤 1 中创建的 Windows Server 2016 映像部署 VM 并安装资源提供程序。
* 注册映射到你的资源提供程序 VM 的本地 DNS 记录。
* 注册资源提供程序与本地 Azure 资源管理器 （租户和管理员）。


可以：
- 在命令行上指定至少所需的参数
- 或者，如果运行不带任何参数，则输入系统提示时。

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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数
你可以在命令行中指定这些参数。 如果你不希望这样做，或任何参数验证失败，系统会提示你提供所需的。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 有关访问特权终结点所需的云管理员凭据。 | （必需） |
| **AzCredential** | 提供 Azure 堆栈服务管理员帐户的凭据。 使用相同的凭据用于部署 Azure 堆栈）。 | （必需） |
| **VMLocalCredential** | 定义 MySQL 资源提供程序 VM 的本地管理员帐户的凭据。 | （必需） |
| **PrivilegedEndpoint** | 提供的 IP 地址或特权终结点的 DNS 名称。 |  （必需） |
| **DependencyFilesLocalPath** | 本地共享包含路径[mysql 连接器 net 6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi)。 如果你提供一个，证书文件必须放置在此目录中。 | _可选_(_必需_多节点) |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码 | （必需） |
| **MaxRetryCount** | 定义你想要重试每个操作，如果有多少次失败。| 2 |
| **RetryDuration** | 定义重试，以秒为单位之间的超时值。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源 （请参阅下面的注释） | 否 |
| **DebugMode** | 阻止失败的自动清理 | 否 |
| **AcceptLicense** | 跳过的提示时接受 GPL 许可证 (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


具体取决于系统的性能和下载速度，安装可能需要最小为 20 分钟或长时间为几个小时。 如果 MySQLAdapter 边栏选项卡不可用，刷新管理门户。

> [!NOTE]
> 如果安装需要超过 90 分钟，则可能会失败，并在屏幕上，在日志文件中，你将看到失败消息。 从失败的步骤重试部署。 不符合建议的内存和核心规范的系统可能不能部署 MySQL RP。



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>验证使用 Azure 堆栈门户部署

> [!NOTE]
>  安装脚本完成后，你将需要刷新门户后，以查看管理边栏选项卡。


1. 服务管理员身份登录到管理门户。

2. 验证部署成功完成。 浏览**资源组** &gt;，单击**系统。\<位置\>.mysqladapter**资源组，并验证所有四个部署成功。

      ![验证 MySQL RP 的部署](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>提供通过连接到 MySQL 宿主服务器的容量

1. 登录到 Azure 堆栈门户为服务管理员。

2. 浏览到**管理资源** &gt; **MySQL 宿主服务器** &gt; **+ 添加**。

    **MySQL 宿主服务器**边栏选项卡是其中你可以连接到的 MySQL Server 作为资源提供程序的后端的实际实例的 MySQL Server 资源提供程序。

    ![宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. 使用您的 MySQL Server 实例的连接详细信息来填充窗体。 提供的完全限定的域名 (FQDN) 或有效的 IPv4 地址，并不是短 VM 名称。 此安装不再提供的默认 MySQL 实例。 提供的大小可帮助管理数据库容量资源提供程序。 它应接近数据库服务器的物理容量。

    > [!NOTE]
    > 只要租户和管理 Azure 资源管理器可以访问 MySQL 实例，它可以放置受控制的资源提供程序。 MySQL 实例__必须__分配以独占方式给 RP。

4. 添加服务器时，你必须为他们分配一种新的或现有的 SKU，以允许服务产品的不同之处。
  例如，你可以具有企业实例提供：
    - 数据库容量
    - 自动备份
    - 预留各部门的高性能服务器
 

SKU 名称应反映的属性，以便租户可以适当地将其数据库。 在 SKU 中的所有宿主服务器应具有相同的功能。

![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> Sku 可以花费一个小时的时间无法在门户中显示。 无法创建一个数据库，直到创建 SKU。


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>若要测试你的部署，创建你的第一个 MySQL 数据库


1. 登录到 Azure 堆栈门户为服务管理员。

2. 单击**+ 新建**按钮&gt;**数据 + 存储** &gt; **MySQL 数据库**。

3. 填写数据库详细信息的表单。

    ![创建测试 MySQL 数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 选择一种的 SKU。

    ![选择一种的 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. 创建一个登录名设置。 可以重复使用的登录名设置或创建一个新。 此设置包含的用户名称和数据库的密码。

    ![创建新的数据库登录名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    该连接字符串包含实际的数据库服务器名称。 从门户复制它。

    ![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> 用户名称的长度不能超过 32 个字符带有 MySQL 5.7 或早期版本中的 16 个字符。


## <a name="add-capacity"></a>添加容量

通过在 Azure 堆栈门户中添加其他 MySQL 服务器添加容量。 其他服务器可以添加到一种新的或现有的 SKU。 请确保服务器特征都相同。


## <a name="make-mysql-databases-available-to-tenants"></a>使 MySQL 数据库可供租户使用
创建计划，并提供可使 MySQL 数据库供租户可用。 添加 Microsoft.MySqlAdapter 服务，添加一个配额，等等。

![创建计划，并提供要包括数据库](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>更新管理密码
可以通过第一个更改它的 MySQL server 实例上修改密码。 浏览到**管理资源** &gt; **MySQL 宿主服务器**&gt;单击的宿主服务器上。 在设置面板中，单击密码。

![更新管理员密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="remove-the-mysql-resource-provider-adapter"></a>删除 MySQL 资源提供程序适配器

若要删除资源提供程序，请务必首先删除任何依赖关系。

1. 请确保您具有原始你下载的此版本的资源提供程序的部署包。

2. 从资源提供程序 （这不会删除数据），必须删除所有租户数据库。 这应该通过租户本身。

3. 从命名空间中，租户必须取消注册。

4. 管理员必须从 MySQL 适配器中删除宿主服务器

5. 管理员必须删除任何引用 MySQL 适配器的计划。

6. 管理员必须删除与 MySQL 适配器相关联的任何配额。

7. 重新运行的部署脚本的卸载参数、 Azure 资源管理器终结点、 DirectoryTenantID 和服务管理员帐户凭据。




## <a name="next-steps"></a>后续步骤

尝试使用其他[PaaS 服务](azure-stack-tools-paas-services.md)如[SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)和[应用程序服务资源提供程序](azure-stack-app-service-overview.md)。
