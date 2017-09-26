---
title: "将 Windows 计算机连接到 Azure Log Analytics | Microsoft 文档"
description: "本文介绍了通过使用自定义版本的 Microsoft Monitoring Agent (MMA) 将本地基础结构中的 Windows 计算机连接到 Log Analytics 服务的步骤。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 48a0eaeb10d406d551c9e5870edde06809bd7544
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017

---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>将 Windows 计算机连接到 Azure 中的 Log Analytics 服务

本文介绍了通过使用自定义版本的 Microsoft Monitoring Agent (MMA) 将本地基础结构中的 Windows 计算机连接到 OMS 工作区的步骤。 需要为想要登录的所有计算机安装代理并进行连接，以便代理向 Log Analytics 服务发送数据以及查看和操作该数据。 每个代理都可以向多个工作区报告。

可使用安装程序、命令行或 Azure 自动化中的所需状态配置 (DSC) 安装代理。  

>[!NOTE]
为使虚拟机在 Azure 上运行，可使用[虚拟机扩展](log-analytics-azure-vm-extension.md)简化安装。

在连接 Internet 的计算机上，代理使用 Internet 连接向 OMS 发送数据。 对于没有连接 Internet 的计算机，可使用代理或 [OMS 网关](log-analytics-oms-gateway.md)。

将 Windows 计算机连接到 OMS 非常简单，只需三个简单步骤：

1. 从 OMS 门户下载代理安装程序文件
2. 使用所选方法安装代理
3. 如有需要，配置代理或添加额外的工作区

以下图表显示在安装并配置代理后，Windows 计算机与 OMS 之间的关系。

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

如果 IT 安全策略不允许网络上的计算机连接到 Internet，可以将计算机配置为连接到 OMS 网关。 有关如何将服务器配置为通过 OMS 网关与 OMS 服务进行通信的详细信息和步骤，请参阅[使用 OMS 网关将计算机连接到 OMS](log-analytics-oms-gateway.md)。

## <a name="system-requirements-and-required-configuration"></a>系统要求和所需配置
安装或部署代理之前，请查看以下详细信息以确保操作满足要求。

- 只能在运行 Windows Server 2008 SP 1 或更高版本或者运行 Windows 7 SP1 或更高版本的计算机上安装 OMS MMA。
- 需要一个 Azure 订阅。  有关详细信息，请参阅 [Log Analytics 入门](log-analytics-get-started.md)。
- 每台 Windows 计算机必须能够连接到 Internet（使用 HTTPS）或 OMS 网关。 可直接、通过代理或 OMS 网关完成此连接。
- 可在独立计算机、服务器和虚拟机上安装 OMS MMA。 如果想要将 Azure 托管的虚拟机连接到 OMS，请参阅[将 Azure 虚拟机连接到 Log Analytics](log-analytics-azure-vm-extension.md)。
- 代理需要针对各种资源使用 TCP 端口 443。

### <a name="network"></a>网络

若要让 Windows 代理连接和注册到 OMS 服务，必须允许其访问包括端口号和域 URL 在内的网络资源。

- 对于代理服务器，需确保在代理设置中配置适当的代理服务器资源。
- 对于会限制 Internet 访问的防火墙，你或者你的网络工程师需将防火墙配置为允许访问 OMS。 代理设置中不需要任何操作。

下表显示通信所需的资源。

>[!NOTE]
>以下某些资源提及了操作见解，这是 Log Analytics 的早期名称。

| 代理资源 | 端口 | 绕过 HTTPS 检查 |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | 是 |
| *.oms.opinsights.azure.com | 443 | 是 |
| *.blob.core.windows.net | 443 | 是 |
| *.azure-automation.net | 443 | 是 |



## <a name="download-the-agent-setup-file-from-oms"></a>从 OMS 下载代理安装程序文件
1. 在 OMS 门户的“**概述**”页面上，单击“**设置**”磁贴。  单击顶部的“**相连的源**”选项卡。  
    ![“相连的源”选项卡](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. 单击“Windows 服务器”，然后单击适用于计算机处理器类型的“下载 Windows 代理”，以下载安装程序文件。
3. 在“工作区 ID”右侧单击复制图标，并将该 ID 粘贴到记事本中。
4. 在“主键”右侧单击复制图标，并将该键粘贴到记事本中。     

## <a name="install-the-agent-using-setup"></a>使用安装程序安装代理
1. 在想要管理的计算机上运行安装程序以安装代理。
2. 在“欢迎”页面上，单击“**下一步**”。
3. 在“许可条款”页面上阅读许可协议，然后单击“**我接受**”。
4. 在“目标文件夹”页面上更改或保留默认安装文件夹，然后单击“**下一步**”。
5. 在“代理安装程序选项”页面上，可选择将代理连接到 Azure Log Analytics (OMS)、Operations Manager，如果想要稍后配置代理，也可将选项保留为空。 单击“下一步”。   
    - 如果选择连接到 Azure Log Analytics (OMS)，粘贴在前面的过程中复制到记事本中的**工作区 ID** 和**工作区键（主键）**，然后单击“**下一步**”。  
        ![粘贴工作区 ID 和主键](./media/log-analytics-windows-agents/connect-workspace.png)
    - 如果选择连接到 Operations Manager，请键入**管理组名称**、**管理服务器**名称和**管理服务器端口**，然后单击“**下一步**”。 在“代理操作帐户”页面上，选择本地系统帐户或本地域帐户，然后单击“**下一步**”。  
        ![管理组配置](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![代理操作帐户](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. 在“准备安装”页面上，查看已选内容，然后单击“**安装**”。
7. 在“配置已成功完成”页面上，单击“**完成**”。
8. 完成后，**Microsoft Monitoring Agent** 将显示在“**控制面板**”中。 可在该处查看配置并验证代理是否已连接到 Operational Insights (OMS)。 如果连接到 OMS，代理会显示一条消息，指出：**Microsoft Monitoring Agent 已成功连接到 Microsoft Operations Management Suite 服务。**

## <a name="configure-proxy-settings"></a>配置代理设置

可通过以下过程，使用控制面板为 Microsoft Monitoring Agent 配置代理设置。 需要对每个服务器使用此过程。 如果需要配置多台服务器，使用脚本自动执行此过程可能更加轻松。 如果是，请参阅下一过程：[使用脚本配置 Microsoft Monitoring Agent 的代理设置](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script)。

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>使用控制面板为 Microsoft Monitoring Agent 配置代理设置
1. 打开“控制面板”
2. 打开“Microsoft Monitoring Agent”
3. 单击“代理设置”选项卡。  
    ![“代理设置”选项卡](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. 选择“使用代理服务器”并键入 URL 和端口号（如果需要），如以下示例所示。 如果你的代理服务器要求身份验证，请键入用户名和密码以访问代理服务器。


### <a name="verify-agent-connectivity-to-oms"></a>验证代理能否连接到 OMS

可以通过以下过程轻松验证代理是否与 OMS 通信：

1.  在装有 Windows 代理的计算机上，打开控制面板。
2.  打开 Microsoft Monitoring Agent。
3.  单击“Azure Log Analytics (OMS)”选项卡。
4.  在“状态”列中，应该会看到代理成功连接到 Operations Management Suite 服务。

![代理已连接](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>使用脚本配置 Microsoft Monitoring Agent 的代理设置
复制以下示例，使用特定于你的环境的信息进行更新，保存并使其带有 PS1 文件扩展名，然后在直接连接到 OMS 服务的每台计算机上运行该脚本。

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)



## <a name="install-the-agent-using-the-command-line"></a>使用命令行安装代理
- 修改后参照以下示例使用命令行安装代理。 该示例执行完全无提示安装。

    >[!NOTE]
    如果想要升级代理，需要使用 Log Analytics 脚本 API。 请参阅下一节升级代理。

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

该代理通过 `/c` 命令使用 IExpress 作为其自解压缩程序。 可以查看 [IExpress 的命令行开关](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)中的命令行开关，然后根据需要更新示例。

|特定于 MMA 的选项                   |说明         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = 将代理配置为向工作区报告                |
|OPINSIGHTS_WORKSPACE_ID                | 要添加的工作区的工作区 ID (GUID)                    |
|OPINSIGHTS_WORKSPACE_KEY               | 工作区密钥，用于通过工作区进行初始身份验证 |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | 指定工作区所在的云环境 <br> 0 = Azure 商业云（默认值） <br> 1 = Azure 政府 |
|OPINSIGHTS_PROXY_URL               | 要使用的代理的 URI |
|OPINSIGHTS_PROXY_USERNAME               | 要访问的经过身份验证的代理用户名 |
|OPINSIGHTS_PROXY_PASSWORD               | 要访问的经过身份验证的代理密码 |

>[!NOTE]
为了避免命中 IExpress 的命令行长度限制，请安装未配置工作区的代理，然后使用脚本设置工作区的配置。

>[!NOTE]
如果在使用 `OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE` 参数时收到`Command line option syntax error.`，可以使用以下解决办法：
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>使用脚本添加工作区
可通过以下示例使用 Log Analytics 代理脚本 API 添加工作区：

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

若要在适用于美国政府的 Azure 中添加工作区，请使用以下脚本示例：
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
如果以前已使用命令行或脚本安装或配置代理，`EnableAzureOperationalInsights` 会被 `AddCloudWorkspace` 取代。

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>使用 Azure 自动化中的 DSC 安装代理

可通过以下脚本示例，使用 Azure 自动化中的 DSC 安装代理。 该示例安装由 `URI` 值标识的 64 位代理。 还可通过替换 URI 值，使用 32 位版本。 这两个版本的 URI 分别是：

- Windows 64 位代理 - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 位代理 - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
此过程和脚本示例不会升级现有代理。

1. 从 [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 将 xPSDesiredStateConfiguration DSC 模块导入 Azure 自动化。  
2.  为 *OPSINSIGHTS_WS_ID* 和 *OPSINSIGHTS_WS_KEY* 创建 Azure 自动化变量资产。 将 *OPSINSIGHTS_WS_ID* 设置为 OMS Log Analytics 工作区 ID，将 *OPSINSIGHTS_WS_KEY* 设置为工作区的主键。
3.  请使用以下脚本，将其另存为 MMAgent.ps1
4.  修改后参照以下示例使用 Azure 自动化中的 DSC 安装代理。 通过使用 Azure 自动化界面或 cmdlet 将 MMAgent.ps1 导入 Azure 自动化。
5.  向配置分配节点。 15 分钟内，该节点将检查其配置，MMA 将被推送到该节点。

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

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
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>获取最新的 ProductId 值

对于每个代理版本，MMAgent.ps1 脚本中的 `ProductId value` 是唯一的。 发布每个代理的更新版本时，ProductId 值会更改。 因此，当 ProductId 在将来发生更改时，可使用简单脚本来查找代理版本。 在测试服务器上安装最新代理版本后，可使用以下脚本来获取已安装的 ProductId 值。 使用最新的 ProductId 值，可更新 MMAgent.ps1 脚本中的值。

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>手动配置代理或添加额外的工作区
如果已安装代理但没有进行配置，或者希望代理向多个工作区报告，可使用以下信息来启用代理或重新配置代理。 配置代理后，会在代理服务中注册代理，并会获得必要的配置信息和包含解决方案信息的管理包。

1. 安装 Microsoft Monitoring Agent 后，打开“**控制面板**”。
2. 打开 **Microsoft Monitoring Agent**，然后单击“**Azure Log Analytics (OMS)**”选项卡。   
3. 单击“**添加**”以打开“**添加 Log Analytics 工作区**”框。
4. 为想要添加的工作区粘贴在前面的过程中复制到记事本中的**工作区 ID** 和**工作区键（主键）**，然后单击“**确定**”。  
    ![配置 Operational Insights](./media/log-analytics-windows-agents/add-workspace.png)

收集代理所监视的计算机中的数据后，在 OMS 门户中，OMS 监视的计算机数将在“**设置**”中“**相连的源**”选项卡上显示为“**已连接的服务器数**”。


## <a name="to-disable-an-agent"></a>禁用代理
1. 安装代理后，打开“**控制面板**”。
2. 打开 Microsoft Monitoring Agent，然后单击“**Azure Log Analytics (OMS)**”选项卡。
3. 选择工作区，然后单击“**移除**”。 对所有其他工作区重复此步骤。


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>可选择将代理配置为向 Operations Manager 管理组报告

如果在 IT 基础结构中使用 Operations Manager，还可以使用 MMA 代理作为 Operations Manager 代理。

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>将 MMA 代理配置为向 Operations Manager 管理组报告
1.  在安装代理的计算机上打开“**控制面板**”。  
2.  打开 **Microsoft Monitoring Agent**，然后单击“**Operations Manager**”选项卡。  
    ![Microsoft Monitoring Agent Operations Manager 选项卡](./media/log-analytics-windows-agents/om-mg01.png)
3.  如果 Operations Manager 服务器与 Active Directory 集成，请单击“**自动更新 AD DS 中的管理组分配**”。
4.  单击“**添加**”以打开“**添加管理组**”对话框。  
    ![Microsoft Monitoring Agent 添加管理组](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  在“**管理组名称**”框中，键入管理组的名称。
6.  在“**主管理服务器**”框中，键入主管理服务器的计算机名称。
7.  在“**管理服务器端口**”框中，键入 TCP 端口号。
8.  在“**代理操作帐户**”下面，选择本地系统帐户或本地域帐户。
9.  单击“**确定**”关闭“**添加管理组**”对话框中，再单击“**确定**”关闭“**Microsoft Monitoring Agent 属性**”对话框。


## <a name="next-steps"></a>后续步骤

- [从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)，以添加功能和收集数据。

