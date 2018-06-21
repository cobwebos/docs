---
title: 使用 Azure 堆栈上的 MySQL 数据库 |Microsoft 文档
description: 了解如何可以作为 Azure 堆栈和快速的步骤，以部署 MySQL Server 资源提供程序适配器服务部署 MySQL 数据库。
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295752"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 MySQL 数据库
使用 Azure 堆栈 MySQL Server 资源提供程序作为 Azure 堆栈的服务公开 MySQL 数据库。 在 MySQL 资源提供程序 VM，这是 Windows Server 内核的虚拟机上运行的 MySQL 资源提供程序服务。

## <a name="deploy-the-resource-provider"></a>部署资源提供程序 
1. 如果尚未这样做，请注册开发工具包，并通过市场管理下载 Windows Server 2016 Datacenter Core 映像。 必须使用 Windows Server 2016 Core 映像。 也可以使用脚本创建 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。 （请务必选择“Core”选项）。 

2. 登录到可以访问特权终结点 VM 的主机：
    - 在 Azure SDK 安装中，登录到物理主机。  
    - 在集成的系统上的主机必须是一个系统，可以访问特权终结点。 
    
    >[!NOTE] 
    > 运行脚本的系统必须是装有最新版 .NET 运行时的 Windows 10 或 Windows Server 2016 系统。 否则安装会失败。 Azure 堆栈 ASDK 主机都能满足此条件。 
    
3. 下载 MySQL 资源提供程序二进制文件。 然后运行自解压程序，将内容解压缩到临时目录。 
    >[!NOTE]  
    > 资源提供程序有一个相应的 Azure Stack 最低内部版本。 请务必下载适用于运行中 Azure Stack 版本的正确二进制文件。

    | Azure 堆栈版本 | MySQL RP 版本| 
    | --- | --- | 
    | 版本 1804 (1.0.180513.1)|[MySQL RP 版本 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | 版本 1802 (1.0.180302.1) | [MySQL RP 版本 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | 版本 1712 （1.0.180102.3 或 1.0.180106.1 （集成系统）） | [MySQL RP 版本 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  为 ASDK 中,，将创建一个自签名的证书作为此过程的一部分。 对于集成系统，必须提供相应的证书。 如需提供自己的证书，请将满足以下条件的 .pfx 文件放在 **DependencyFilesLocalPath** 中： 
    - \*.dbadapter.\<区域\>.\<外部 FQDN\> 的通配符证书，或采用 mysqladapter.dbadapter.\<区域\>.\<外部 FQDN\> 公用名的单个站点证书。 
    - 此证书必须受信任。 也就是说，信任链必须存在，且不需要中间证书。 
    - DependencyFilesLocalPath 中只存在单个证书文件。 
    - 文件名不能包含任何特殊字符或空格。 

5. 打开权限提升的（管理）**新** PowerShell 控制台。 然后切换到文件解压缩到的目录。 使用新窗口可以避免因系统上已加载的错误 PowerShell 模块可能造成的问题。 

6. 运行**DeployMySqlProvider.ps1**脚本。 该脚本执行以下步骤： 
    - 下载 MySQL 连接器二进制文件（可脱机提供）。 
    - 将证书和其他项目上传到 Azure Stack 上的存储帐户。 
    - 发布库包，以便通过库部署 SQL 数据库。 
    - 发布用于部署宿主服务器的库包。 
    - 部署使用 Windows Server 2016 Azure 堆栈应用商店映像的 VM 并安装资源提供程序。 
    - 注册映射到资源提供程序 VM 的本地 DNS 记录。 
    - 将资源提供程序注册到本地 Azure 资源管理器（租户和管理员）。 

    你可以指定命令行上所需的参数或运行不带任何参数，该脚本，然后输入系统提示时。 

    下面是可从 PowerShell 命令提示符运行的示例命令。 请务必根据需要更改帐户信息和密码： 

    ```powershell 
    # Install the AzureRM.Bootstrapper module and set the profile. 
    Install-Module -Name AzureRm.BootStrapper -Force 
    Use-AzureRmProfile -Profile 2017-03-09-profile 

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
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
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

> [!NOTE] 
> SKU 最长可能需要在一小时后才显示在门户中。 在创建 SKU 之前，无法创建数据库。 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure Stack 门户验证部署 
> [!NOTE] 
>  在安装脚本完成运行之后，必须刷新门户才能看到管理边栏选项卡。 

1. 以服务管理员身份登录到管理门户。 
2. 验证部署是否成功。 转到“资源组”，然后选择“system.\<位置\>.mysqladapter”资源组。 验证所有四个部署已成功：

      ![验证 MySQL RP 的部署](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>后续步骤
[添加宿主服务器](azure-stack-mysql-resource-provider-hosting-servers.md)
