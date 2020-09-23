---
title: 连接到 Azure Stack Edge Pro GPU 设备上的 Azure 资源管理器
description: 介绍如何使用 Azure PowerShell 连接到 Azure Stack Edge Pro GPU 上运行的 Azure 资源管理器。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5cf406dc0577f477858dd8a6570f7975747112e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891267"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>连接到 Azure Stack Edge Pro 设备上的 Azure 资源管理器

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure 资源管理器提供了一个管理层，使你能够在 Azure 订阅中创建、更新和删除资源。 Azure Stack Edge Pro 设备支持相同的 Azure 资源管理器 Api 来创建、更新和删除本地订阅中的 Vm。 此支持使你能够以与云一致的方式管理设备。 

本教程介绍如何使用 Azure PowerShell 通过 Azure 资源管理器连接到 Azure Stack Edge Pro 设备上的本地 Api。

## <a name="about-azure-resource-manager"></a>关于 Azure 资源管理器

Azure 资源管理器提供一致的管理层来调用 Azure Stack Edge Pro 设备 API 并执行创建、更新和删除 Vm 等操作。 下图详细说明了 Azure 资源管理器的体系结构。

![Azure 资源管理器关系图](media/azure-stack-edge-j-series-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 设备上的终结点

下表总结了设备上公开的各种终结点、支持的协议以及用于访问这些终结点的端口。 在本文中，你将找到对这些终结点的引用。

| # | 终结点 | 支持的协议 | 使用的端口 | 用途 |
| --- | --- | --- | --- | --- |
| 1. | Azure 资源管理器 | https | 443 | 连接到 Azure 资源管理器以实现自动化 |
| 2. | 安全令牌服务 | https | 443 | 通过访问和刷新令牌进行身份验证 |
| 3. | Blob | https | 443 | 通过 REST 连接到 Blob 存储 |


## <a name="connecting-to-azure-resource-manager-workflow"></a>连接到 Azure 资源管理器工作流

使用 Azure 资源管理器连接到设备的本地 Api 的过程需要执行以下步骤：

| 步骤编号 | 你将执行此步骤 .。。 | .. 在此位置。 |
| --- | --- | --- |
| 1. | [配置 Azure Stack Edge Pro 设备](#step-1-configure-azure-stack-edge-pro-device) | 本地 Web UI |
| 2. | [创建和安装证书](#step-2-create-and-install-certificates) | Windows 客户端/本地 web UI |
| 3. | [查看和配置先决条件](#step-3-install-powershell-on-the-client) | Windows 客户端 |
| 4. | [设置客户端上的 Azure PowerShell](#step-4-set-up-azure-powershell-on-the-client) | Windows 客户端 |
| 5. | [修改主机文件以进行终结点名称解析](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows 客户端或 DNS 服务器 |
| 6. | [检查终结点名称是否已解析](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows 客户端 |
| 7. | [使用 Azure PowerShell cmdlet 验证连接到 Azure 资源管理器](#step-7-set-azure-resource-manager-environment) | Windows 客户端 |

以下部分详细介绍了连接到 Azure 资源管理器的上述每个步骤。

## <a name="prerequisites"></a>必备知识

在开始之前，请确保用于通过 Azure 资源管理器连接到设备的客户端使用的是 TLS 1.2。 有关详细信息，请参阅 [在 Windows 客户端上配置 TLS 1.2，访问 Azure Stack Edge Pro 设备 "](azure-stack-edge-j-series-configure-tls-settings.md)。

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>步骤1：配置 Azure Stack Edge Pro 设备 

在 Azure Stack Edge Pro 设备的本地 web UI 中执行以下步骤。

1. 完成 Azure Stack Edge Pro 设备的网络设置。 

    ![本地 Web UI“网络设置”页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    记下设备 IP 地址。 稍后将使用此 IP。

2. 从 " **设备** " 页配置设备名称和 DNS 域。 记下设备名称和 DNS 域，因为稍后将用到它们。

    ![本地 Web UI“设备”页](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > 设备名称、DNS 域将用于形成公开的终结点。
    > 使用本地 web UI 中 " **设备** " 页上的 "Azure 资源管理器和 Blob 终结点"。


## <a name="step-2-create-and-install-certificates"></a>步骤2：创建和安装证书

证书确保你的通信受到信任。 在 Azure Stack Edge Pro 设备上，自动生成自签名的设备、blob 和 Azure 资源管理器证书。 此外，也可以将自己的已签名 blob 和 Azure 资源管理器证书。

当你引入自己的签名证书时，你还需要证书的相应签名链。 对于签名链、Azure 资源管理器和设备上的 blob 证书，你将需要客户端计算机上的相应证书，以便对设备进行身份验证和通信。

若要连接到 Azure 资源管理器，你将需要创建或获取签名链和终结点证书，在 Windows 客户端上导入这些证书，最后在设备上上传这些证书。

### <a name="create-certificates-optional"></a> (可选) 创建证书

仅适用于测试和开发，你可以使用 Windows PowerShell 在你的本地系统上创建证书。 为客户端创建证书时，请遵循以下准则：

1. 首先需要为签名链创建根证书。 有关详细信息，请参阅 [创建签名链证书](azure-stack-edge-j-series-manage-certificates.md#create-signing-chain-certificate)的步骤。

2. 接下来，可以为 blob 和 Azure 资源管理器创建终结点证书。 可以从本地 web UI 中的 " **设备** " 页获取这些终结点。 请参阅 [创建终结点证书](azure-stack-edge-j-series-manage-certificates.md#create-signed-endpoint-certificates)的步骤。

3. 对于所有这些证书，请确保 "使用者名称" 和 "使用者备用名称" 符合以下准则：

    |类型 |使用者名称 (SN)   |使用者可选名称 (SAN)   |使用者名称示例 |
    |---------|---------|---------|---------|
    |Azure 资源管理器|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 存储|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |两个终结点的多 SAN 单一证书|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

有关证书的详细信息，请参阅如何 [管理证书](azure-stack-edge-j-series-manage-certificates.md)。

### <a name="upload-certificates-on-the-device"></a>在设备上上传证书

你在上一步中创建的证书将位于客户端上的 "个人" 存储中。 需要将这些证书导出到你的客户端，然后才能将其上传到你的设备。

1. 根证书必须导出为带有 *.cer* 文件扩展名的 DER 格式文件。 有关详细步骤，请参阅 [将证书导出为 .cer 格式文件](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-der-format)。

2. 终结点证书必须导出为包含私钥的 *.pfx* 文件。 有关详细步骤，请参阅将 [证书导出为带有私钥的 .pfx 文件](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)。

3. 然后，在本地 web UI 中使用 "**证书**" 页上的 " **+ 添加证书**" 选项，在设备上上传根证书和终结点证书。 若要上载证书，请按照 [上载证书](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)中的步骤进行操作。


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>在运行 Azure PowerShell 的客户端上导入证书

你将在其中调用 Azure 资源管理器 Api 的 Windows 客户端需要建立与设备的信任。 为此，必须将在上一步中创建的证书导入到相应的证书存储中。

1. 现在应在客户端系统上的 "受信任的根证书颁发机构" 中导入作为带有 *.cer* 扩展名的 DER 格式导出的根证书。 有关详细步骤，请参阅 [将证书导入到受信任的根证书颁发机构存储。](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format)

2. 作为 *.pfx* 导出的终结点证书必须导出为 *.cer*。 然后，此 *.cer* 将导入到系统上的 " **个人** " 证书存储中。 有关详细步骤，请参阅将 [证书导入到个人存储](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format)中。

## <a name="step-3-install-powershell-on-the-client"></a>步骤3：在客户端上安装 PowerShell 

你的 Windows 客户端必须满足以下先决条件：

1. 运行 PowerShell 版本5.0。 必须安装有 PowerShell 5.0 版或更高版本。 若要检查系统上的 PowerShell 版本，请运行以下 cmdlet：

    ```powershell
    $PSVersionTable.PSVersion
    ```

    比较 **主要** 版本，并确保其为5.0 或更高版本。

    如果版本已过时，请参阅[升级现有的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)。

    如果 \' 没有 PowerShell 5.0，请遵循 [安装 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6)。

    下面显示了示例输出。

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. 可以访问 PowerShell 库。

    以管理员身份运行 PowerShell。 验证 PSGallery 是否已注册为存储库。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    下面显示了示例输出。
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
如果你的存储库不受信任，或者需要详细信息，请参阅 [验证 PowerShell 库辅助功能](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?view=azs-1908#2-validate-the-powershell-gallery-accessibility)。

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>步骤4：在客户端上设置 Azure PowerShell 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. 你将在客户端上安装 Azure PowerShell 模块，这些模块将适用于你的设备。

    a. 以管理员身份运行 PowerShell。 需要访问 PowerShell 库。 


    b. 若要从 PowerShell 库安装所需 Azure PowerShell 模块，请运行以下命令：

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    请确保在安装结束时运行 Azure RM 模块版本2.5.0。 
    如果你的现有版本的 Azure RM 模块与所需的版本不匹配，请使用以下命令卸载：

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    现在需要重新安装所需的版本。
   

下面显示了一个示例输出，指示已成功安装 AzureRM 版本2.5.0 模块。

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>步骤5：修改主机文件以进行终结点名称解析 

现在会将在设备的本地 web UI 上定义的 Azure 一致 VIP 添加到：

- 客户端上的主机文件，或
- DNS 服务器配置

> [!IMPORTANT]
> 建议你修改用于终结点名称解析的 DNS 服务器配置。

请在用于连接到设备的 Windows 客户端上执行以下步骤：

1. 以管理员身份启动 **记事本** ，然后打开位于 C:\Windows\System32\Drivers\etc. 的 **hosts** 文件。

    ![Windows 资源管理器 hosts 文件](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)

2. 将以下条目添加到 hosts 文件，替换为设备的适当值： 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > 主机文件中的条目应完全匹配提供的以连接到 Azure 资源管理器后面的步骤。 请确保此处的 DNS 域条目都采用小写形式。

    你已在前面的步骤中保存了本地 web UI 中的设备 IP。

    登录 \<appliance name\> 名。\<DNS domain\> 条目是 (STS) 的安全令牌服务的终结点。 STS 负责创建、验证、续订和取消安全令牌。 Security token service 用于创建访问令牌和刷新令牌，用于设备与客户端之间的连续通信。

3. 请使用以下图像作为参考。 保存 **hosts** 文件。

    ![记事本中的 hosts 文件](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>步骤6：验证客户端上的终结点名称解析

检查终结点名称是否在用于连接到 Azure 一致 VIP 的客户端上解析。

1. 你可以使用 ping.exe 命令行实用程序来检查终结点名称是否已解析。 给定 IP 地址后，ping 命令将返回您重新跟踪的计算机的 TCP/IP 主机名 \' 。

    将 `-a` 开关添加到命令行，如下面的示例中所示。 如果主机名为可再用，则它还会在回复中返回这一重要的信息。

    ![命令提示符中的 Ping](media/azure-stack-edge-j-series-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>步骤7：设置 Azure 资源管理器环境

设置 Azure 资源管理器环境，并验证你的设备到通过 Azure 资源管理器的客户端通信是否正常工作。 执行此验证的步骤如下：


1. 使用 `Add-AzureRmEnvironment` cmdlet 可以进一步确保通过 azure 资源管理器进行的通信正常工作，并且 API 调用会经历专用于 Azure 资源管理器-443 的端口。

    `Add-AzureRmEnvironment`Cmdlet 将添加终结点和元数据，以使 azure 资源管理器 cmdlet 能够与新的 azure 资源管理器实例连接。 


    > [!IMPORTANT]
    > 在以下 cmdlet 中提供的 Azure 资源管理器终结点 URL 区分大小写。 确保端点 URL 都以小写形式提供，并且与在 hosts 文件中提供的 URL 相匹配。 如果大小写不匹配，则会出现错误。

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    下面显示了示例输出：
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. 将环境设置为 Azure Stack Edge Pro，将 Azure 资源管理器调用的端口设置为443。 可以通过两种方式定义环境：

    - 设置环境。 键入以下命令：

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    有关详细信息，请参阅 [get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0)。

    - 为您执行的每个 cmdlet 定义内联环境。 这可确保所有 API 调用都通过正确的环境。 默认情况下，调用将通过 Azure 公共，但你希望这些调用通过为 Azure Stack Edge Pro 设备设置的环境完成。

    - 有关 [如何切换 AzureRM 环境](#switch-environments)的详细信息，请参阅。

2. 调用本地设备 Api 对 Azure 资源管理器的连接进行身份验证。 

    1. 这些凭据适用于本地计算机帐户，仅用于 API 访问。

    2. 可以通过 `login-AzureRMAccount` 或通过命令进行连接 `Connect-AzureRMAccount` 。 

        1. 若要登录，请键入以下命令。 此实例中的租户 ID 是硬编码-c0257de7-538f-415c-993a-1b87a031879d。 使用以下用户名和密码。

            - **用户名**  - *EdgeArmUser*

            - **密码**  - [设置 Azure 资源管理器的密码](azure-stack-edge-j-series-set-azure-resource-manager-password.md)，并使用此密码进行登录。 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            另一种登录方法是使用 `login-AzureRmAccount` cmdlet。 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -TenantId c0257de7-538f-415c-993a-1b87a031879d 

            下面是该命令的示例输出。 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> 与 Azure 资源管理器的连接每1.5 小时过期一次，或者 Azure Stack Edge Pro 设备重新启动。 如果发生这种情况，你执行的任何 cmdlet 都将返回错误消息，表明你未连接到 Azure。 你需要重新登录。

## <a name="switch-environments"></a>切换环境

运行 `Disconnect-AzureRmAccount` 命令以切换到不同的 `AzureRmEnvironment` 。 

如果使用 `Set-AzureRmEnvironment` 和 `Login-AzureRmAccount` 而不使用 `Disconnect-AzureRmAccount` ，则不会实际切换环境。  

下面的示例演示如何在两个环境（和）之间进行切换 `AzDBE1` `AzDBE2` 。

首先，在客户端上列出现有的所有环境。


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

接下来，通过 Azure 资源管理器获取当前连接到的环境。

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

在切换到其他环境之前，你现在应断开与当前环境的连接。


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

登录到其他环境。 下面显示了示例输出。

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

运行此 cmdlet 可确认连接到的环境。

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
现在，你已切换到预期环境。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack Edge Pro 设备上部署 vm](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)。
