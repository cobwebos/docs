---
title: "在 Azure Functions 中使用数据包捕获执行主动网络监视 | Microsoft 文档"
description: "本文介绍如何使用 Azure 网络观察程序创建警报触发的数据包捕获"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5f31b24a0d46b9d557a55b3c9d0cd7748ecb9c33
ms.lasthandoff: 03/21/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>在 Azure Functions 中使用数据包捕获执行主动网络监视

网络观察程序数据包捕获功能可以创建捕获会话来跟踪传入和传出虚拟机的流量。 可在捕获文件中包含一个定义的筛选器，以便只跟踪想要监视的流量。 然后，将此数据存储在存储 Blob 中或来宾计算机本地。 可以通过 Azure Functions 等其他自动化方案远程启动此功能。 数据包捕获提供基于定义的网络异常运行主动捕获的功能。 其他用途包括收集网络统计信息、获取网络入侵信息、调试客户端与服务器之间的通信，等等。

Azure 中部署的资源全天候运行， 但你或你的同事无法全天候主动监视所有资源的状态。 如果凌晨 2 点出现问题，会发生什么情况？

在 Azure 生态系统中使用网络观察程序、警报和 Functions，可以主动针对网络中的问题做出响应，并使用数据和工具来解决问题。

## <a name="before-you-begin"></a>开始之前

在本示例中，VM 发送的 TCP 段数超过平常，出现问题时你希望能够收到警报。 TCP 段只是用作示例，我们可以使用任何警报条件。 收到警报时，你希望获得数据包级别的数据来了解通信量为何提高，以便可以采取措施让计算机恢复日常通信。
本方案假设已安装网络观察程序的现有实例，并且使用一个包含有效虚拟机的资源组。

## <a name="scenario"></a>方案

为了自动完成此过程，我们在 VM 上创建并连接了一个发生事件时要触发的警报，同时创建了一个用于调用网络观察程序的 Azure 函数。

在此方案中：

* 创建一个启动数据包捕获的 Azure 函数。
* 在虚拟机上创建警报规则
* 将警报规则配置为调用该 Azure 函数

## <a name="creating-an-azure-function-and-overview"></a>创建 Azure 函数和操作概述

第一步是创建一个 Azure 函数来处理警报并创建数据包捕获。 

以下列表了提供发生的工作流概述。

1. 在 VM 上触发警报。
1. 该警报通过 Webhook 调用 Azure 函数。
1. Azure 函数处理警报，并启动网络观察程序数据包捕获会话。
1. 数据包捕获在 VM 上运行并收集流量。 
1. 将捕获文件上载到存储帐户进行审查和诊断 

可以遵循[创建第一个 Azure 函数](../azure-functions/functions-create-first-azure-function.md)，在门户中创建 Azure 函数。 在本示例中，我们选择了 HttpTrigger-PowerShell 类型函数。 自定义项是此示例所必需的，并在以下步骤中说明：

![函数示例][functions1]

> [!NOTE]
> PowerShell 模板处于试验阶段且没有完全支持。

## <a name="adding-modules"></a>添加模块

若要使用网络观察程序 PowerShell cmdlet，需要将最新 PowerShell 模块上载到 Function App。

1. 在已安装最新 Azure PowerShell 模块的本地计算机上，运行以下 PowerShell 命令：

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    这将提供 Azure PowerShell 模块的本地路径。 在稍后的步骤中将使用这些文件夹。 此方案中使用的模块包括：

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![powershell 文件夹][functions5]

1. 导航到“Function App 设置” > “转到应用服务编辑器”。

    ![函数 kudu][functions2]

1. 右键单击 AlertPacketCapturePowershell 文件夹，并创建一个名为 **azuremodules** 的文件夹。 继续为所需的每个模块创建子文件夹。

    ![函数 kudu][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. 右键单击 **AzureRM.Network** 子文件夹，然后单击“上载文件”。 导航到 Azure 模块的安装位置，在本地 AzureRM.Network 文件夹中选择该文件夹中的所有文件，然后单击“确定”。  对 AzureRM.Profile 和 AzureRM.Resources 重复这些步骤。

    ![上载文件][functions6]

1. 完成后，每个文件夹应具有来自本地计算机的 PowerShell 模块文件。

    ![powershell 文件][functions7]

## <a name="authentication"></a>身份验证

如何使用 PowerShell cmdlet，必须进行身份验证。 需要在 Function App 中配置身份验证。 为此，将配置环境变量，并需要将加密密钥文件上载到 Function App。

> [!note]
> 此方案仅提供如何使用 Azure Functions 实现身份验证的一个示例，还可以通过其他方法来执行此操作。

### <a name="encrypted-credentials"></a>加密凭据

以下 PowerShell 脚本创建了一个名为 **PassEncryptKey.key** 的密钥文件，并提供了所提供密码的加密版本。  此密码是为用于身份验证的 Azure AD 应用程序定义的同一密码。

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

在 Function App 的应用服务编辑器中，在 **AlertPacketCapturePowerShell** 下创建一个名为 **keys** 的文件夹，并上载前面的 PowerShell 示例所创建的 **PassEncryptKey.key** 文件。

![函数密钥][functions8]

### <a name="retrieving-values-for-environment-variables"></a>检索环境变量的值

最终必需的配置是设置访问用于身份验证的值所需的环境变量。 下面是创建的环境变量的列表。

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

客户端 ID 是 Azure Active Directory 中应用程序的应用程序 ID。

1. 如果你还没有应用程序可使用，请运行以下示例以创建应用程序。

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > 创建应用程序时使用的密码应与先前在保存密钥文件时创建的密码相同。

1. 在 Azure 门户中，导航到“订阅”> 选择要使用的订阅 >“访问控制(IAM)”。

    ![函数 iam][functions9]

1. 选择要使用的帐户并单击“属性”。 复制应用程序 ID。

    ![函数应用程序 ID][functions10]

#### <a name="azuretenant"></a>AzureTenant

通过运行以下 PowerShell 示例获取租户 ID：

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

AzureCredPassword 环境变量的值是通过运行以下 PowerShell 示例获得的值。 此示例是前面的**加密凭据**部分中显示的同一示例。 所需的值是 `$Encryptedpassword` 变量的输出。  这是我们使用 PowerShell 脚本加密的服务主体密码。

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="storing-the-environment-variables"></a>存储环境变量

1. 导航到 Function App，单击“Function App 设置” > “配置应用设置”

    ![配置应用设置][functions11]

1. 将环境变量及其值添加到应用设置并单击“保存”

    ![应用程序设置][functions12]

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>处理警报并启动数据包捕获会话

现在，可以从 Azure 函数内部调用网络观察程序。 根据要求，此函数的实现有所不同。 但是，代码的常规流程大致如下：

1. 处理输入参数
2. 查询现有的数据包捕获，验证限制并解决名称冲突
3. 使用适当的参数创建数据包捕获
4. 定期轮询数据包捕获，直到完成
5. 通知用户数据包捕获会话已完成

以下示例采用 PowerShell 语言，可在 Azure 函数中使用。 有需要为 subscriptionId、resourceGroupName 和 storageAccountName 替换的值。

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

创建函数后，需要将警报配置为调用与该函数相关联的 URL。 若要获取此值，请单击“</> 获取函数 URL” 

![查找函数 URL 1][functions13]

复制 Function App 的函数 URL。

![查找函数 URL 2][2]

如果需要在 webhook POST 请求的有效负载中使用自定义属性，请参阅[针对 Azure 指标警报配置 webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="configure-an-alert-on-a-vm"></a>在 VM 上配置警报

可以配置警报，以便在特定的指标超过分配的阈值时通知相关人员。 在本示例中，警报是针对 TCP 段发送的，但也可以从其他许多指标触发该警报。 在本示例中，已将某个警报配置为调用 Webhook 来调用函数。

### <a name="create-the-alert-rule"></a>创建警报规则

导航到现有虚拟机并添加警报规则。 有关配置警报的更详细文档，请参阅 [User Azure portal to create alerts for Azure services](../monitoring-and-diagnostics/insights-alerts-portal.md)（在用户 Azure 门户中为 Azure 服务创建警报）。 

![将 VM 警报规则添加到虚拟机][1]

> [!NOTE]
> 某些指标默认未启用。 请访问[启用监视和诊断](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)，详细了解如何启用其他指标

最后，将前面步骤中获取的 URL 粘贴到警报中的 Webhook 文本框。 单击“确定”保存警报规则。

![将 URL 粘贴到警报规则][3]

## <a name="downloading-and-viewing-the-capture-file"></a>下载并查看捕获文件

如果将捕获保存到了存储帐户，可以通过门户或以编程方式下载捕获文件。 如果捕获文件存储在本地，可以通过登录到虚拟机来检索捕获文件。 

有关从 Azure 存储帐户下载文件的说明，请参阅[通过 .NET 开始使用 Azure Blob 存储](../storage/storage-dotnet-how-to-use-blobs.md)。 可以使用的另一个工具是存储资源管理器。 有关存储资源管理器的详细信息可以在此链接中找到：[存储资源管理器](http://storageexplorer.com/)

下载捕获后，可以使用能够读取 **.cap** 文件的任何工具来查看捕获。 下面提供了其中两个工具的链接：

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>后续步骤

访问 [Packet capture analysis with Wireshark](network-watcher-alert-triggered-packet-capture.md)（使用 Wireshark 分析数据包捕获），了解如何查看数据包捕获

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
