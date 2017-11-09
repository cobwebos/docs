---
title: "管理 Windows Azure 包虚拟机从 Azure 堆栈 |Microsoft 文档"
description: "了解如何从 Azure 堆栈中的用户门户中管理 Windows Azure Pack (WAP) Vm。"
services: azure-stack
documentationcenter: 
author: walterov
manager: byronr
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: walterov
ms.openlocfilehash: b07a18055d149e20cd605a892063eccecf3df8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>从 Azure 堆栈管理 Windows Azure 包虚拟机

*适用范围： Azure 堆栈开发工具包*

在 Azure 堆栈开发工具包中，你可以启用从 Azure 堆栈用户门户，以便租户在 Windows Azure Pack 上运行的虚拟机的访问。 用户可以使用 Azure 堆栈门户来管理其现有的 IaaS 虚拟机和虚拟网络。 这些资源可用于在 Windows Azure 包通过基础 Service Provider Foundation (SPF) 和 Virtual Machine Manager (VMM) 组件。 具体而言，用户可以：

* 浏览资源
* 检查配置值
* 停止或启动虚拟机
* 连接到虚拟机通过远程桌面协议 (RDP)
* 创建和管理检查点
* 删除虚拟机和虚拟网络

Windows Azure 包连接器 Azure 堆栈 （预览版） 提供此功能。 这篇文章演示如何为单节点 Azure 堆栈环境配置连接器。

对于连接器此预览版本，请注意以下事项：
* 仅在测试环境 （对于 Azure 堆栈和 Windows Azure 包），并且不在生产部署，请使用 Windows Azure 包连接器。
* 你必须拥有 Windows Azure 包更新汇总 (UR) 9.1 或更高版本以及 System Center SPF 以及 VMM UR 9 或更高版本。
* VMM 和 SPF 组件可以是 System Center 2012 R2 或 System Center 2016。
* 这两个 Azure 堆栈上和在 Windows Azure 包，则必须执行配置步骤。
* 说明适用于非-云平台系统 CPS 环境。
* 若要查看已知的问题，请参阅[Microsoft Azure 堆栈疑难解答](azure-stack-troubleshooting.md)。


## <a name="architecture"></a>体系结构
下图显示 Windows Azure 包连接器的主要组件。

![Windows Azure 包连接器组件](media/azure-stack-manage-wap/image1.png)

请注意以下详细信息：
* 从这两个云中 （Azure 堆栈和 Windows Azure 包），Azure 堆栈用户门户访问的资源信息。
* 用户必须具有在这两个环境中有效的帐户。
* Azure 堆栈用户门户必须具有到在 Windows Azure Pack 上运行的组件的网络访问权限。
* 在**WAP**部分的关系图中，你可以看到新的 Windows Azure 包连接器模块 （WAP 扩展和连接器） 和 SPF 和 VMM 组件与现有 Windows Azure 包租户 API。


## <a name="identity-management"></a>身份管理
Windows Azure 包租户 API 必须信任 Azure 堆栈安全令牌服务 (STS)。

当用户执行的操作通过 Azure 堆栈门户针对 Windows Azure Pack 资源时，门户将使用 Windows Azure 包租户 API。 因此，提供的用户身份验证令牌必须来自受信任的 STS。 请参阅下图：

![Windows Azure 包连接器身份验证的图示](media/azure-stack-manage-wap/image2.png)

在开发工具包环境中，Windows Azure Pack 和 Azure 堆栈具有独立的标识提供程序。 这两个标识提供程序中，从 Azure 堆栈门户访问这两个环境的用户必须具有相同的用户主体名称 (UPN) 名称。 例如，帐户 *azurestackadmin@azurestack.local* 还应存在于 Windows Azure Pack 的 STS。 在 AD FS 不设置以支持出站信任关系，将 AD FS 的 Azure 堆栈实例从 Windows Azure Pack 组件 (租户 API) 建立信任关系。

## <a name="prerequisites"></a>必备组件

### <a name="download-the-windows-azure-pack-connector"></a>下载 Windows Azure 包连接器
上[Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc)，请下载的.exe 文件，并将其解压缩到本地计算机。 更高版本，你的内容复制到可以访问你的 Windows Azure Pack 环境的计算机。

### <a name="deployment-option-requirement"></a>部署选项要求
若要将与 Windows Azure Pack 集成，你可以通过使用 AD FS 选项或 Azure Active Directory 选项部署 Azure 堆栈。

### <a name="connectivity-requirements"></a>连接要求
1. 从在其访问 Azure 堆栈用户门户，请确保计算机可以通过 web 浏览器访问 Windows Azure Pack 租户门户。
2. Azure 堆栈上的 AzS WASP01 虚拟机必须能够连接到 Windows Azure Pack 租户门户计算机。 使用 Ping.exe 验证网络连接。 
3.  你必须有新的连接器服务的有效证书。 这些 SSL 证书必须由受信任的证书颁发机构 (CA) 颁发。 不能使用自签名的证书。 SSL 证书必须受 Azure 堆栈 (专门 AzS WASP01 VM) 和租户可能用于访问 Azure 堆栈用户门户的任何其他计算机。
 
    >[!NOTE]
    因为 AzS WASP01 运行 Windows Server 服务器核心安装选项，你可以使用 Certutil.ext 之类的命令行工具导入证书。 例如，你无法将.cer 文件复制到 c:\temp 上 AzS WASP01，，，然后运行该命令**certutil addstore"CA""c:\temp\certname.cer"**。

4.  若要建立与通过 Azure 堆栈门户的 Windows Azure Pack 租户虚拟机的 RDP 连接，Windows Azure Pack 环境必须允许到租户虚拟机的远程桌面流量。
5.  有关 Azure 堆栈租户虚拟机与 Windows Azure Pack 租户虚拟机之间的连接，其外部的 IP 地址必须是可路由的两种环境。 此连接可能还包含关联的 DNS 服务器解析环境之间的虚拟机名称。

### <a name="iis-requirements"></a>IIS 要求
承载 Windows Azure Pack 租户门户 （它可能是物理主机、 虚拟机或多个虚拟机） 的计算机必须安装了 URL 重写 IIS 扩展。 如果尚未安装，你可以下载它从[此处](https://www.iis.net/downloads/microsoft/url-rewrite)。 如果多个虚拟机托管租户门户，请在每个虚拟机上安装扩展。

## <a name="configure-azure-stack"></a>配置 Azure 堆栈
在配置 Windows Azure 包连接器之前，您必须启用 Azure 堆栈用户门户中的多云模式。 此模式下使用户能够选择从哪些云访问资源。

若要启用多云模式，你必须在 Azure 堆栈部署后运行添加 AzurePackConnector.ps1 脚本。 下表介绍的脚本参数。


|  参数 | 说明 | 示例 |   
| -------- | ------------- | ------- |  
| AzurePackClouds | Windows Azure 包连接器的 Uri。 这些 Uri 应对应于 Windows Azure Pack 租户门户。 | @{CloudName ="AzurePack1";CloudEndpoint ="https://waptenantportal1:40005"},@{CloudName ="AzurePack2";CloudEndpoint ="https://waptenantportal2:40005"}<br><br>  （默认情况下，将端口值为 40005。） |  
| AzureStackCloudName | 来表示本地 Azure 堆栈云的标签。| "AzureStack" |
| DisableMultiCloud | 要禁用多云模式的交换机。| 不适用 |
| | |

立即部署后，或更高版本，您可以运行添加 AzurePackConnector.ps1 脚本。 若要在部署后立即运行脚本，使用同一个 Windows PowerShell 会话，其中 Azure 堆栈部署完成。 否则，你可以打开新的 Windows PowerShell 会话以管理员身份 （azurestackadmin 帐户作为登录）。

1. 使用以下命令 （使用特定于你环境的值） 运行添加 AzurePackConnector.ps1 脚本。 请注意添加 AzurePackConnector 脚本使您可以添加多个 Windows Azure 包连接器终结点。
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> 当前生成中没有问题其中添加 AzurePackConnector 脚本结束后，它仍处于轮询循环时间 （几分钟时间） 的长时间直到结束。 你看到的消息后**VERBOSE： 步骤配置 Azure Pack 连接器状态： '为 Success'**，可以停止该脚本，也可以等待，直到它停止本身。 它将不会起作用，因为已成功配置。

2. 记下此脚本，一个用于指定每个 Windows Azure Pack 环境生成的输出文件。 文件位于： \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput。 这些文件包含配置目标 Windows Azure Pack 环境所需的信息。 你会将此文件作为参数传递到更高版本中这些说明的脚本中。 此文件包含以下信息：

    * **AzurePackConnectorEndpoint**： 包含到 Windows Azure 包连接器服务终结点。
    * **AuthenticationIdentityProviderPartner**： 包含以下值对：
        * 身份验证令牌签名需要信任以接受来自 Azure 堆栈门户扩展调用 Windows Azure 包租户 API 的证书。

        * "领域"与签名证书。 例如： https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/。

3.  浏览到包含输出文件的文件夹 (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput)，并将文件复制到本地计算机。 文件将类似于此： AzurePack-06-27-15-50.txt。

4.  测试配置。

    a. 打开浏览器并登录到 Azure 堆栈用户门户 (https://portal.local.azurestack.external/)。
    
    b. 以租户和门户负载登录后，你将看到关于未能够从 Azure Pack 云提取订阅或扩展的错误。 单击**确定**关闭这些消息。 （这些错误消息将会消失后配置 Windows Azure 包。）

    c. 请注意**云**门户的左上角的下拉列表。

    ![Azure 堆栈用户界面中的云选择器](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>配置 Windows Azure 包
对于此连接器仅限预览版本，Windows Azure 包要求手动配置。

>[!IMPORTANT]
对于此预览版中，使用 Windows Azure 包连接器仅在测试环境中，不能在生产部署。

1.  安装 Windows Azure Pack 租户门户在虚拟机上，连接器 MSI 文件和安装证书。 （如果你有多个租户门户虚拟机，你必须完成此步骤中的每个虚拟机上）。

    a. 在文件资源管理器，复制**WAPConnector**文件夹 （什么前面下载） 到名为的文件夹**c:\temp**租户门户虚拟机中。

    b. 打开到租户门户虚拟机的控制台或 RDP 连接。

    c. 将目录更改为**c:\temp\wapconnector\setup\scripts**，并运行**安装 Connector.ps1**脚本来安装三个 MSI 文件。 不不需要任何参数。

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d.单击“下一步”。 将目录更改为**c:\inetpub**并验证是否安装了三个新站点：

       * MgmtSvc 连接器

       * MgmtSvc ConnectorExtension

       * MgmtSvc ConnectorController

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 从同一个**c:\temp\wapconnector\setup\scripts**文件夹中，运行**配置 Certificates.ps1**脚本来安装证书。 默认情况下，它将使用相同的证书可用于在 Windows Azure Pack 租户门户网站。 请确保这是 （Azure 堆栈 AzS WASP01 虚拟机和访问 Azure 堆栈门户任何客户端计算机信任） 的有效证书。 否则，通信不起作用。 (或者，你可以将显式传递证书指纹作为参数使用-指纹参数。)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. 若要完成这三项服务的配置，运行**配置 WapConnector.ps1**脚本以更新 Web.config 文件参数。

    |  参数 | 说明 | 示例 |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Windows Azure Pack 租户门户 FQDN。 | tenant.contoso.com | 
    | TenantAPIFQDN | Windows Azure 包租户 API FQDN。 | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Azure 堆栈用户门户 FQDN。 | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. 如果你有多个租户门户虚拟机，请为每个这些虚拟机重复步骤 1。

2. 在每个 Windows Azure 包租户 API 虚拟机上安装新的租户 API MSI。

    a. 如果负载平衡器正在使用中，你可能想要将标记为脱机虚拟机。

    b. 在文件资源管理器，复制**WAPConnector**到名为的文件夹的文件夹**c:\temp**每个租户 API 计算机上。

    c. 更早版本，保存的 AzurePackConnectorOutput.txt 文件复制到**c:\temp\WAPConnector**。

    d.单击“下一步”。 打开控制台或 RDP 连接到租户 API 虚拟机复制到文件。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 将目录更改为**c:\temp\wapconnector\setup\scripts**，并运行**更新 TenantAPI.ps1**。 此新版本的 WAP 租户 API 包含的更改，以启用信任关系使用当前的 STS，不仅能还与 Azure 堆栈中的 AD FS 的实例。

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  重复步骤 2 运行租户 API 的任何其他虚拟机上。
3. 从**只有一个**的租户 API 虚拟机，运行用于添加 Azure 堆栈上的租户 API 和 AD FS 实例之间的信任关系的配置 TrustAzureStack.ps1 脚本。 你必须使用具有访问 Microsoft.MgmtSvc.Store 数据库的 sysadmin 权限的帐户。 此脚本具有以下参数：

    | 参数 | 说明 | 示例 |
    | --------- | ------------| ------- |
   | Sql Server | 包含 Microsoft.MgmtSvc.Store 数据库的 SQL server 名称。 此参数是必需的。 | Sql Server | 
   | 数据文件 | 已在更早版本的 Azure 堆栈多云模式的配置过程中生成输出文件。 此参数是必需的。 | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | 指示该脚本应提示你以交互方式输入要连接到 SQL Server 实例时使用的 SQL 身份验证凭据。 给定的凭据必须具有足够的权限来卸载数据库，架构，以及删除用户登录名。 如果未提供，该脚本假设该当前用户上下文具有访问权限。 | 需要没有值。 |
   |  |  |

    如果你不知道要使用的 SQL 服务器，你可能会发现它。 连接到租户 API 计算机，请使用 Unprotect MgmtSvc 命令取消租户 API Web.config 文件中，并查找的连接字符串中的服务器名称。 请务必先运行保护-MgmtSvc 再次以防租户 API Web.config 文件。

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>示例
下面的示例显示完整的 Windows Azure 包连接器部署在单节点 Azure 堆栈配置和这两个 Windows Azure Pack Express 安装。 (每个快速安装位于一台计算机，使用的示例名称*wapcomputer1*和*wapcomputer2*。)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
下载中的.exe 文件[Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc)，解压缩它，并复制到的 WAPConnector 文件夹**c:\temp** Windows Azure 包计算机上的文件夹。 将复制作为前一个脚本中的输出生成的文件 (位于\\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) 到**c:\temp\WAPConnector**文件夹。 (文件将如下所示类似于此： AzurePack-06-27-15-50.txt。)然后，运行以下脚本，一次每个 Windows Azure Pack:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>故障排除提示
1.  确保 Azure 堆栈和 Windows Azure 包之间的网络连接。 此连接应为访问 Azure 堆栈门户任何租户计算机和 Windows Azure Pack 租户门户虚拟机之间运行新的连接器服务的位置。
2.  请确保所有指定的 Fqdn 正确。
3.  确保在新的连接器服务中使用的 SSL 证书所信任的 Azure 堆栈 (专门 AzS WASP01 VM) 和任何其他计算机租户可能用于访问 Azure 堆栈用户门户。
4. 有关已知问题，请参阅[Microsoft Azure 堆栈疑难解答](azure-stack-troubleshooting.md)。


## <a name="next-steps"></a>后续步骤
[在 Azure 堆栈中使用的管理员和用户门户](azure-stack-manage-portals.md)
