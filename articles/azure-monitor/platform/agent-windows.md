---
title: 在 Windows 计算机上安装 Log Analytics 代理
description: 本文介绍如何使用适用于 Windows 的 Log Analytics 代理将在其他云中或本地托管的 Windows 计算机连接到 Azure Monitor。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: d283c2b2cdbbeb3ef4bc4e25f4288dfd95158552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003365"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>在 Windows 计算机上安装 Log Analytics 代理
本文详细介绍如何使用以下方法在 Windows 计算机上安装 Log Analytics 代理：

* 使用 [安装向导](#install-agent-using-setup-wizard) 或 [命令行](#install-agent-using-command-line)进行手动安装。
* [Azure 自动化所需状态配置 (DSC) ](#install-agent-using-dsc-in-azure-automation)。 

>[!IMPORTANT]
> 本文中所述的安装方法通常用于本地或其他云中的虚拟机。 有关可用于 Azure 虚拟机的更高效的选项，请参阅 [安装选项](log-analytics-agent.md#installation-options) 。

> [!NOTE]
> 如果需要将代理配置为向多个工作区报告，则不能在初始设置期间执行此操作，而只能在之后通过从控制面板或 PowerShell 更新设置执行，如[添加或删除工作区](agent-manage.md#adding-or-removing-a-workspace)中所述。  

## <a name="supported-operating-systems"></a>支持的操作系统

有关 Log Analytics 代理支持的 Windows 版本的列表，请参阅 [Azure Monitor 代理概述](agents-overview.md#supported-operating-systems) 。

### <a name="sha-2-code-signing-support-requirement"></a>SHA-1 代码签名支持要求 
Windows 代理将于 2020 年 8 月 17 日开始以独占方式使用 SHA-2 签名。 此更改将影响使用旧版 OS 上的 Log Analytics 代理作为任何 Azure 服务（Azure Monitor、Azure 自动化、Azure 更新管理、Azure 更改跟踪、Azure 安全中心、Azure Sentinel、Windows Defender ATP）一部分的客户。 除非你在旧版 OS 版本（Windows 7、Windows Server 2008 R2 和 Windows Server 2008）上运行代理，否则更改不需要任何客户操作。 在 2020 年 8 月 17 日之前，在旧版 OS 版本上运行的客户必须在其计算机上执行以下操作，否则其代理会停止将数据发送到其 Log Analytics 工作区：

1. 为 OS 安装最新服务器包。 必需的服务包版本包括：
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. 要为 OS 安装 SHA-2 签名 Windows 更新，请参阅[适用于 Windows 和 WSUS 的 2019 SHA-2 代码签名支持要求](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. 更新到 Windows 代理的最新版本（版本 10.20.18029）。
4. 建议将代理配置为[使用 TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12)。 

## <a name="network-requirements"></a>网络要求
请参阅 [Log Analytics 代理概述](log-analytics-agent.md#network-requirements) ，了解 Windows 代理的网络要求。


   
## <a name="configure-agent-to-use-tls-12"></a>将代理配置为使用 TLS 1.2
[TLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12) 协议确保传输中数据的安全性，以便在 Windows 代理和 Log Analytics 服务之间进行通信。 如果在 [默认情况下未启用 tls 1.2 的操作系统](data-security.md#sending-data-securely-using-tls-12)上安装，则应使用以下步骤配置 tls 1.2。

1. 找到以下注册表子项：**HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. 在“Protocols”下为 TLS 1.2 创建一个子项：HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2
3. 在你之前创建的 TLS 1.2 协议版本子项下创建一个 **Client** 子项。 例如，HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client。
4. 在 HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client 下创建以下 DWORD 值：

    * **Enabled** [值 = 1]
    * **DisabledByDefault** [值 = 0]  

将 .NET Framework 4.6 或更高版本配置为安全加密，因为默认情况下禁用此功能。 [强加密](/dotnet/framework/network-programming/tls#schusestrongcrypto)使用更安全的网络协议（例如 TLS 1.2）并且会阻止不安全的协议。 

1. 找到以下注册表子项：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**。  
2. 在此子项下创建值为 **1**的 DWORD 值 **SchUseStrongCrypto**。  
3. 找到以下注册表子项：**HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**。  
4. 在此子项下创建值为 **1**的 DWORD 值 **SchUseStrongCrypto**。 
5. 重启系统以使设置生效。 

## <a name="install-agent-using-setup-wizard"></a>使用安装向导安装代理
以下步骤在计算机上使用代理的设置向导在 Azure 和 Azure 政府云中安装并配置 Log Analytics 代理。 如果希望了解如何将代理配置为也向 System Center Operations Manager 管理组进行报告，请参阅[使用代理设置向导部署 Operations Manager 代理](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)。

1. 在 Log Analytics 工作区中，从先前导航到的“Windows 服务器”页，根据 Windows 操作系统的处理器体系结构选择相应的“下载 Windows 代理”版本。    
2. 运行安装程序在计算机上安装该代理。
2. 在“欢迎”页面上，单击“下一步”。 
3. 在“许可条款”页面上阅读许可协议，然后单击“我接受” 。
4. 在“目标文件夹”页面上更改或保留默认安装文件夹，然后单击“下一步” 。
5. 在“代理安装选项”页上，选择将代理连接到 Azure Log Analytics，单击“下一步”。    
6. 在“Azure Log Analytics”页上执行以下操作：
   1. 粘贴前面复制的“工作区 ID”和“工作区密钥(主密钥)”。   如果计算机应向 Azure 政府云中的 Log Analytics 工作区报告，请从“Azure 云”下拉列表中选择“Azure 美国政府版”。   
   2. 如果计算机需要通过代理服务器来与 Log Analytics 通信，请单击“高级”并提供代理服务器的 URL 和端口号。  如果代理服务器要求身份验证，请键入用于在代理服务器上进行身份验证的用户名和密码，并单击“下一步”。  
7. 提供所需的配置设置后，单击“下一步”。<br><br> ![粘贴工作区 ID 和主键](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. 在“准备安装”页上检查所做的选择，并单击“安装”。 
9. 在“配置已成功完成”页上，单击“完成”。 

完成后，**Microsoft Monitoring Agent** 将显示在“**控制面板**”中。 要确认其正在向 Log Analytics 报告，请参阅[验证代理与 Log Analytics 的连接](#verify-agent-connectivity-to-azure-monitor)。 

## <a name="install-agent-using-command-line"></a>使用命令行安装代理
下载的代理文件是自包含安装包。  代理和支持文件的安装程序包含在该包中，需要提取才能使用以下示例中所示的命令行正确安装。    

>[!NOTE]
>如果想要升级代理，需要使用 Log Analytics 脚本 API。 有关详细信息，请参阅[管理并维护 Windows 和 Linux 的 Log Analytics 代理](agent-manage.md)。

下表突出显示了代理的安装程序支持的特定参数，包括使用 Automation DSC 进行部署的时间。

|特定于 MMA 的选项                   |说明         |
|---------------------------------------|--------------|
| NOAPM=1                               | 可选参数。 安装不带 .NET 应用程序性能监视的代理。|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = 将代理配置为向工作区报告                |
|OPINSIGHTS_WORKSPACE_ID                | 要添加的工作区的工作区 ID (GUID)                    |
|OPINSIGHTS_WORKSPACE_KEY               | 工作区密钥，用于通过工作区进行初始身份验证 |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | 指定工作区所在的云环境 <br> 0 = Azure 商业云（默认值） <br> 1 = Azure 政府 |
|OPINSIGHTS_PROXY_URL               | 要使用的代理的 URI |
|OPINSIGHTS_PROXY_USERNAME               | 要访问的经过身份验证的代理用户名 |
|OPINSIGHTS_PROXY_PASSWORD               | 要访问的经过身份验证的代理密码 |

1. 若要提取代理安装文件，请在提升的命令提示符处运行 `MMASetup-<platform>.exe /c`，这会提示要将文件提取到的路径。  或者，可以通过传递参数 `MMASetup-<platform>.exe /c /t:<Full Path>` 来指定路径。  
2. 要以无提示方式安装代理，并将其配置为向 Azure 商业版云中的工作区报告，请在提取安装文件的文件夹中键入： 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   或者，要将代理配置为向 Azure 美国政府版云报告，请键入： 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >需要将参数 OPINSIGHTS_WORKSPACE_ID 和 OPINSIGHTS_WORKSPACE_KEY 的字符串值封装在双引号中，以指示 Windows Installer 将其解释为包的有效选项。 

## <a name="install-agent-using-dsc-in-azure-automation"></a>使用 Azure 自动化中的 DSC 安装代理

可通过以下脚本示例，使用 Azure Automation DSC 安装代理。   如果没有自动化帐户，请在使用 Automation DSC 前查看 [Azure 自动化入门](../../automation/index.yml)，了解创建自动化账户的需求和步骤。  如果不熟悉 Automation DSC，请参阅 [Automation DSC 入门](../../automation/automation-dsc-getting-started.md)。

下面的示例安装由 `URI` 值标识的 64 位代理。 还可通过替换 URI 值，使用 32 位版本。 这两个版本的 URI 分别是：

- Windows 64 位代理 - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 位代理 - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>此过程和脚本示例不支持升级已部署到 Windows 计算机的代理。

32 位和 64 位版本的代理包具有不同的产品代码，新发布的版本也具有唯一的产品代码。  产品代码是一个 GUID，它是应用程序或产品的主体标志，由 Windows Installer 的“ProductCode”属性表示。  **MMAgent.ps1** 脚本中的 `ProductId` 值必须与 32 位或 64 位代理安装程序包的产品代码匹配。

要直接从代理安装包检索产品代码，可使用[适用于 Windows Installer 开发者的 Windows SDK 组件](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers)中的 Orca.exe，该组件是 Windows 软件开发工具包的一个组件，或按照 Microsoft 最有价值专家 (MVP) 编写的[示例脚本](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)来使用 PowerShell。  对于上述任一种方法，都需要先从 MMASetup 安装包中提取 **MOMagent.msi** 文件。  在前面[使用命令行安装代理](#install-agent-using-command-line)部分下的第一个步骤中演示了此操作。  

1. 从 [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 将 xPSDesiredStateConfiguration DSC 模块导入到 Azure 自动化。  
2.    为 *OPSINSIGHTS_WS_ID* 和 *OPSINSIGHTS_WS_KEY* 创建 Azure 自动化变量资产。 将 OPSINSIGHTS_WS_ID 设置为 Log Analytics 工作区 ID，将 OPSINSIGHTS_WS_KEY 设置为工作区的主键 。
3.    复制脚本，并将其另存为 MMAgent.ps1。

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. 按照先前建议的方法，使用从最新版本的代理安装包中提取的产品代码更新脚本中的 `ProductId` 值。 
5. [将 MMAgent.ps1 配置脚本导入](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation)到自动化帐户。 
6. [将 Windows 计算机或节点分配](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration)到配置。 15 分钟内，该节点会检查其配置，代理会被推送到该节点。

## <a name="verify-agent-connectivity-to-azure-monitor"></a>验证代理与 Azure Monitor 的连接

代理安装完毕后，可通过两种方式来验证是否成功连接和完成报告。  

在计算机的“控制面板”中，找到“Microsoft Monitoring Agent”项 。  选择该项，在“Azure Log Analytics”选项卡上，代理应显示一条消息：“Microsoft Monitoring Agent 已成功连接到Microsoft Operations Management Suite 服务”。<br><br> ![MMA 与 Log Analytics 的连接状态](media/agent-windows/log-analytics-mma-laworkspace-status.png)

还可在 Azure 门户中执行简单的日志查询。  

1. 在 Azure 门户中，搜索并选择“监视”。
1. 在菜单中选择“日志”。
1. 在“日志”窗格的查询字段中键入：  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

在返回的搜索结果中，应可以看到计算机的检测信号记录，该记录指示计算机已连接到服务，并向其报告。

## <a name="cache-information"></a>缓存信息

Log Analytics 代理中的数据将缓存在本地计算机上的 *C:\Program Files\Microsoft 监视 Agent\Agent\Health 服务状态* ，然后再发送到 Azure Monitor。 代理每隔20秒尝试上传一次。 如果该操作失败，它将等待呈指数级增加的时间，直到成功为止。 它将在第二次尝试之前等待30秒，在下一次重试之前60秒，120秒，8.5 依此类推，直到再次成功连接。 此等待时间略有随机化，可避免所有代理同时尝试连接。 达到最大缓冲区后，会丢弃最早的数据。

默认缓存大小为 50 MB，但可以配置为最小为 5 MB，最大值为 1.5 GB。 它存储在注册表项 *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum*中。 该值表示页数，每页 8 KB。


## <a name="next-steps"></a>后续步骤

- 查看[管理和维护 Windows 和 Linux 的 Log Analytics 代理](agent-manage.md)以了解如何重新配置、升级代理或从虚拟机中删除代理。

- 如果在安装或管理代理时遇到问题，请查看 [Windows 代理疑难解答](agent-windows-troubleshoot.md)。
