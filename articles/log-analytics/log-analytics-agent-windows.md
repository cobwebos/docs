---
title: 将 Windows 计算机连接到 Azure Log Analytics | Microsoft 文档
description: 本文介绍如何使用 Microsoft Monitoring Agent (MMA) 将在其他云中或本地托管的 Windows 计算机连接到 Log Analytics。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 975b3d30f242b877512312f8a5a030bde9349287
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003641"
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>将 Windows 计算机连接到 Azure 中的 Log Analytics 服务

要使用 Log Analytics 在本地数据中心或其他云环境中监视和管理虚拟机或物理计算机，需部署 Microsoft Monitoring Agent (MMA)，并将其配置为向一个或多个 Log Analytics 工作区报告。  该代理还支持用于 Azure 自动化的混合 Runbook 辅助角色。  

在受到监视的 Windows 计算机上，该代理被列为 Microsoft Monitoring Agent 服务。 Microsoft Monitoring Agent 服务从日志文件和 Windows 事件日志、性能数据及其他遥测数据中收集事件。 即使代理无法与 Log Analytics 服务（其报告对象）保持通信，也会持续在受监视计算机的磁盘上运行收集的数据，并对这些数据进行排队。 还原连接后，Microsoft Monitoring Agent 服务会向该服务发送所收集的数据。

可使用以下方法之一安装代理。 大多数安装采用这些方法的组合，根据实际情况安装不同的计算机组。  有关使用每种方法的详细信息在本文中后面提供。

* 手动安装。 可以使用安装向导通过命令行在计算机上手动运行安装程序，也可以使用现有软件分发工具进行部署。
* Azure 自动化 Desired State Configuration (DSC)。 配合使用 Azure 自动化中的 DSC 以及已部署在环境中的 Windows 计算机的脚本。  
* PowerShell 脚本。
* 适用于在 Azure Stack 中运行 Windows 本地环境的虚拟机的资源管理器模板。  

若要了解支持的配置，请查看[支持的 Windows 操作系统](log-analytics-concept-hybrid.md#supported-windows-operating-systems)和[网络防火墙配置](log-analytics-concept-hybrid.md#network-firewall-requirements)。

## <a name="obtain-workspace-id-and-key"></a>获取工作区 ID 和密钥
在安装适用于 Windows 的 Microsoft Monitoring Agent 之前，需要先获得 Log Analytics 工作区的工作区 ID 和密钥。  安装期间每种安装方法需要此信息才能正确配置代理，并确保它能在 Azure 商业版和美国政府云中与 Log Analytics 成功通信。  

1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在 Log Analytics 工作区列表中，选择要将代理配置为向其报告的工作区。
3. 选择“高级设置”。<br><br> ![Log Analytics 高级设置](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. 选择“已连接的源”，然后选择“Windows 服务器”。   
5. 将**工作区 ID** 和**主密钥**复制并粘贴到常用编辑器。    
   
## <a name="install-the-agent-using-setup-wizard"></a>使用安装向导安装代理
以下步骤使用计算机上 Microsoft Monitoring Agent 的设置向导在 Azure 和 Azure 政府云中安装并配置 Log Analytics 的代理。  

1. 在 Log Analyics 工作区中，从先前导航到的“Windows 服务器”页，根据 Windows 操作系统的处理器体系结构选择相应的“下载 Windows 代理”版本。   
2. 运行安装程序在计算机上安装该代理。
2. 在“欢迎”页面上，单击“下一步”。
3. 在“许可条款”页面上阅读许可协议，然后单击“我接受”。
4. 在“目标文件夹”页面上更改或保留默认安装文件夹，然后单击“下一步”。
5. 在“代理安装选项”页上，选择将代理连接到 Azure Log Analytics (OMS)，单击“下一步”。   
6. 在“Azure Log Analytics”页上执行以下操作：
   1. 粘贴前面复制的“工作区 ID”和“工作区密钥(主密钥)”。  如果计算机应向 Azure 政府云中的 Log Analytics 工作区报告，请从“Azure 云”下拉列表中选择“Azure 美国政府版”。  
   2. 如果计算机需要通过代理服务器来与 Log Analytics 通信，请单击“高级”并提供代理服务器的 URL 和端口号。  如果代理服务器要求身份验证，请键入用于在代理服务器上进行身份验证的用户名和密码，并单击“下一步”。  
7. 提供所需的配置设置后，单击“下一步”。<br><br> ![粘贴工作区 ID 和主键](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. 在“准备安装”页上检查所做的选择，并单击“安装”。
9. 在“配置已成功完成”页上，单击“完成”。

完成后，**Microsoft Monitoring Agent** 将显示在“**控制面板**”中。 要确认其正在向 Log Analytics 报告，请参阅[验证代理与 Log Analytics 的连接](#verify-agent-connectivity-to-log-analytics)。 

## <a name="install-the-agent-using-the-command-line"></a>使用命令行安装代理
下载的代理文件是自包含安装包。  代理和支持文件的安装程序包含在该包中，需要提取才能使用以下示例中所示的命令行正确安装。    

>[!NOTE]
>如果想要升级代理，需要使用 Log Analytics 脚本 API。 有关详细信息，请参阅[管理并维护 Windows 和 Linux 的 Log Analytics 代理](log-analytics-agent-manage.md)。

下表突出显示了代理的安装程序支持的特定 Log Analytics 参数，包括使用 Automation DSC 进行部署的时间。

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

1. 若要提取代理安装文件，请在提升的命令提示符处运行 `MMASetup-<platform>.exe /c`，这会提示要将文件提取到的路径。  或者，可以通过传递参数 `MMASetup-<platform>.exe /c /t:<Path>` 来指定路径。  
2. 要以无提示方式安装代理，并将其配置为向 Azure 商业版云中的工作区报告，请在提取安装文件的文件夹中键入： 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   或者，要将代理配置为向 Azure 美国政府版云报告，请键入： 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>使用 Azure 自动化中的 DSC 安装代理

可通过以下脚本示例，使用 Azure Automation DSC 安装代理。   如果没有自动化帐户，请在使用 Automation DSC 前查看 [Azure 自动化入门](../automation/automation-offering-get-started.md)，了解创建自动化账户的需求和步骤。  如果不熟悉 Automation DSC，请参阅 [Automation DSC 入门](../automation/automation-dsc-getting-started.md)。

下面的示例安装由 `URI` 值标识的 64 位代理。 还可通过替换 URI 值，使用 32 位版本。 这两个版本的 URI 分别是：

- Windows 64 位代理 - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 位代理 - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>此过程和脚本示例不支持升级已部署到 Windows 计算机的代理。

32 位和 64 位版本的代理包具有不同的产品代码，新发布的版本也具有唯一的产品代码。  产品代码是一个 GUID，它是应用程序或产品的主体标志，由 Windows Installer 的“ProductCode”属性表示。  MMAgent.ps1 脚本中的 `ProductId value` 必须与 32 位或 64 位代理安装程序包的产品代码匹配。

要直接从代理安装包检索产品代码，可使用[适用于 Windows Installer 开发者的 Windows SDK 组件](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx)中的 Orca.exe，该组件是 Windows 软件开发工具包的一个组件，或按照 Microsoft 最有价值专家 (MVP) 编写的[示例脚本](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)来使用 PowerShell。  对于上述任一种方法，都需要先从 MMASetup 安装包中提取 **MOMagent.msi** 文件。  在前面[使用命令行安装代理](#install-the-agent-using-the-command-line)部分下的第一个步骤中演示了此操作。  

1. 从 [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 将 xPSDesiredStateConfiguration DSC 模块导入到 Azure 自动化。  
2.  为 *OPSINSIGHTS_WS_ID* 和 *OPSINSIGHTS_WS_KEY* 创建 Azure 自动化变量资产。 将 OPSINSIGHTS_WS_ID 设置为 Log Analytics 工作区 ID，将 OPSINSIGHTS_WS_KEY 设置为工作区的主键。
3.  复制脚本，并将其另存为 MMAgent.ps1

    ```PowerShell
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

4. [将 MMAgent.ps1 配置脚本导入](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation)到自动化帐户。 
5. [将 Windows 计算机或节点分配](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration)到配置。 15 分钟内，该节点会检查其配置，代理会被推送到该节点。

## <a name="verify-agent-connectivity-to-log-analytics"></a>验证代理与 Log Analytics 的连接

代理安装完毕后，可通过两种方式来验证是否成功连接和完成报告。  

在计算机的“控制面板”中，找到“Microsoft Monitoring Agent”项。  选择此项，代理应会在“Azure Log Analytics (OMS)”选项卡上显示一条消息，指出：“Microsoft Monitoring Agent 已成功连接到 Microsoft Operations Management Suite 服务”。<br><br> ![MMA 与 Log Analytics 的连接状态](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

还可在 Azure 门户中执行简单的日志搜索。  

1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。  
2. 在 Log Analytics 工作区页面中，选择一个目标工作区，然后选择“日志搜索”磁贴。 
2. 在“日志搜索”窗格的查询字段中键入：  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

在返回的搜索结果中，应可以看到计算机的检测信号记录，该记录指示计算机已连接到服务，并向其报告。   

## <a name="next-steps"></a>后续步骤

查看[管理并维护 Windows 和 Linux 的 Log Analytics 代理](log-analytics-agent-manage.md)，了解如何在代理部署生命周期内在计算机上管理代理。  
