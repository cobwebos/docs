---
title: 在 Azure Stack 中使用 SQL 数据库 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署 SQL 数据库即服务，并通过便捷的步骤部署 SQL Server 资源提供程序适配器。
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938326"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>部署 Azure 堆栈上的 SQL Server 资源提供程序

使用 Azure 堆栈 SQL Server 资源提供程序作为 Azure 堆栈服务公开 SQL 数据库。 SQL 资源提供程序作为服务运行在 Windows Server 2016 服务器核心虚拟机 (VM) 上。

## <a name="prerequisites"></a>必备组件

需要先实施几个先决条件，然后才能部署 Azure Stack SQL 资源提供程序。 若要满足这些要求，完成以下步骤可以访问特权终结点 VM 的计算机上：

- 如果尚未这样做[注册 Azure 堆栈](.\azure-stack-registration.md)与 Azure 使你可以下载 Azure 应用商店项。
- 通过下载将在所需的 Windows Server core VM 添加到 Azure 堆栈 marketplace **Windows Server 2016 Datacenter 的服务器核心**映像。 也可以使用脚本创建 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 请确保在运行脚本时选择核心选项。

  >[!NOTE]
  >如果你需要安装更新，你可以将单个 MSU 包放在本地依赖关系路径。 如果找到多个 MSU 文件，则 SQL 资源提供程序安装将失败。

- 下载二进制 SQL 资源提供程序，然后运行自解压缩程序中，若要将内容提取到临时目录。 资源提供程序有一个相应的 Azure Stack 最低内部版本。 请确保下载你正在运行的 Azure 堆栈的版本正确的二进制文件。

    |Azure 堆栈版本|SQL RP 版本|
    |-----|-----|
    |版本 1804 (1.0.180513.1)|[SQL RP 版本 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |版本 1802 (1.0.180302.1)|[SQL RP 版本 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |版本 1712 （1.0.180102.3、 1.0.180103.2 或 1.0.180106.1 （集成系统））|[SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>证书

仅适用于集成的系统安装。 你必须提供的可选的 PaaS 证书部分中描述 SQL PaaS PKI 证书[Azure 堆栈部署 PKI 要求](.\azure-stack-pki-certs.md#optional-paas-certificates)。 将.pfx 文件放在指定的位置**DependencyFilesLocalPath**参数。

## <a name="deploy-the-sql-resource-provider"></a>部署 SQL 资源提供程序

你已安装的所有先决条件后，运行**DeploySqlProvider.ps1**脚本部署 SQL 资源提供程序。 DeploySqlProvider.ps1 脚本会提取你下载的你的 Azure 堆栈版本的 SQL 资源提供程序二进制文件的一部分。

> [!IMPORTANT]
> 在运行脚本的系统必须安装的.NET 运行时的最新版本的 Windows 10 或 Windows Server 2016 系统。

若要部署 SQL 资源提供程序，打开**新**提升的 PowerShell 控制台窗口，并将更改为你在哪里提取的 SQL 资源提供程序二进制文件的目录。 我们建议使用新的 PowerShell 窗口，以避免潜在问题引起的已加载 PowerShell 模块。

运行 DeploySqlProvider.ps1 脚本，完成以下任务：

- 将证书和其他项目上传到 Azure Stack 上的存储帐户。
- 发布库包，以便你可以将部署使用库的 SQL 数据库。
- 发布用于部署宿主服务器的库包。
- 将部署使用 Windows Server 2016 core 映像下载，然后再安装 SQL 资源提供程序的 VM。
- 注册映射到资源提供程序 VM 的本地 DNS 记录。
- 注册资源提供程序与本地 Azure 资源管理器的运算符和用户帐户。
- （可选） 在资源提供程序安装过程中安装的单个 Windows Server 更新。

> [!NOTE]
> SQL 资源提供程序部署的启动时，则**system.local.sqladapter**创建资源组。 可能需要最多 75 分钟才能完成到此资源组四个所需的部署。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数

你可以指定以下参数从命令行。 如果不这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_（对于集成系统为强制的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

>[!NOTE]
> SKU 最长可能需要在一小时后才显示在门户中。 之前的 SKU 不部署且正在运行，无法创建一个数据库。

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>使用自定义脚本部署 SQL 资源提供程序

若要部署的资源提供程序时，请消除任何手动配置，你可以自定义以下脚本。 根据需要为你的 Azure 堆栈部署更改的默认帐户信息和密码。

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

资源提供程序安装脚本完成后，请刷新浏览器以确保你可以看到最新的更新。

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署

你可以使用以下步骤验证 SQL 资源提供程序已成功部署。

1. 以服务管理员身份登录到管理门户。
2. 选择**资源组**。
3. 选择**系统。\<位置\>.sqladapter**资源组。
4. 中的消息**部署**下, 一步的屏幕截图中所示，应为**4 Succeeded**。

      ![验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. 你可以获取有关资源提供程序部署更多详细的信息**设置**。 选择**部署**以获取信息，如： 状态、 时间戳和每个部署的持续时间。

## <a name="next-steps"></a>后续步骤

[添加宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)
