---
title: "将 Windows 计算机连接到 Log Analytics | Microsoft Docs"
description: "本文介绍了通过使用自定义版本的 Microsoft Monitoring Agent (MMA) 将本地基础结构中的 Windows 计算机直接连接到 OMS 的步骤。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6836cd4c1f9fe53691ae8330b25e497da4c2e0d5
ms.openlocfilehash: 161bb18579db7a4615cbc62c539e8b6286a424ac


---
# <a name="connect-windows-computers-to-log-analytics"></a>将 Windows 计算机连接到 Log Analytics

本文介绍了通过使用自定义版本的 Microsoft Monitoring Agent (MMA) 将本地基础结构中的 Windows 计算机直接连接到 OMS 工作区的步骤。 你需要为想要登录的所有计算机安装代理并连接到 OMS，以便代理向 OMS 发送数据以及在 OMS 门户中查看和操作该数据。 每个代理都可以向多个工作区报告。

可使用安装程序、命令行或 Azure 自动化中的所需状态配置 (DSC) 安装代理。  

>[!NOTE]
为使虚拟机在 Azure 上运行，可使用[虚拟机扩展](log-analytics-azure-vm-extension.md)简化安装。

在连接 Internet 的计算机上，代理将使用 Internet 连接向 OMS 发送数据。 对于没有连接 Internet 的计算机，可使用代理或 [OMS 网关](log-analytics-oms-gateway.md)。

将 Windows 计算机连接到 OMS 非常简单，只需 3 个简单步骤：

1. 从 OMS 门户下载代理安装程序文件
2. 使用所选方法安装代理
3. 如有需要，配置代理或添加额外的工作区

安装并配置代理后，以下图表显示 Windows 计算机和 OMS 之间的关系。

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>系统要求和所需配置
安装或部署代理之前，请查看以下详细信息以确保操作满足必要的要求。

- 只能在运行 Windows Server 2008 SP 1 或更高版本或者运行 Windows 7 SP1 或更高版本的计算机上只安装 OMS MMA。
- 你将需要 OMS 订阅。  有关更多信息，请参阅 [Log Analytics 入门](log-analytics-get-started.md)。
- 每台 Windows 计算机必须能够连接到 Internet（使用 HTTPS）或 OMS 网关。 可直接、通过代理或 OMS 网关完成此连接。
- 可在独立计算机、服务器和虚拟机上安装 OMS MMA。 如果想要将 Azure 托管的虚拟机连接到 OMS，请参阅[将 Azure 虚拟机连接到 Log Analytics](log-analytics-azure-vm-extension.md)。
- 代理需要针对各种资源使用 TCP 端口 443。 有关详细信息，请参阅[在 Log Analytics 中配置代理服务器和防火墙设置](log-analytics-proxy-firewall.md)。

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
5. 在“代理安装程序选项”页面上，可选择将代理连接到 Operations Manager 中的 Azure Log Analytics (OMS)，或者如果想要稍候配置代理，可将选项保留为空。 单击“资源组名称” 的 Azure 数据工厂。   
    - 如果选择连接到 Azure Log Analytics (OMS)，粘贴在前面的过程中复制到记事本中的**工作区 ID** 和**工作区键（主键）**，然后单击“**下一步**”。  
        ![粘贴工作区 ID 和主键](./media/log-analytics-windows-agents/connect-workspace.png)
    - 如果选择连接到 Operations Manager，请键入**管理组名称**、**管理服务器**名称和**管理服务器端口**，然后单击“**下一步**”。 在“代理操作帐户”页面上，选择本地系统帐户或本地域帐户，然后单击“**下一步**”。  
        ![管理组配置](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![代理操作帐户](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. 在“准备安装”页面上，查看已选内容，然后单击“**安装**”。
7. 在“配置已成功完成”页面上，单击“**完成**”。
8. 完成后，**Microsoft Monitoring Agent** 将显示在“**控制面板**”中。 可在该处查看配置并验证代理是否已连接到 Operational Insights (OMS)。 如果连接到 OMS，代理会显示一条消息，指出：**Microsoft Monitoring Agent 已成功连接到 Microsoft Operations Management Suite 服务。**

## <a name="install-the-agent-using-the-command-line"></a>使用命令行安装代理
- 修改后参照以下示例使用命令行安装代理。

    >[!NOTE]
    如果想要升级代理，需要使用 Log Analytics 脚本 API。 请参阅下一节升级代理。

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>升级代理并使用脚本添加工作区
可通过以下 PowerShell 示例升级代理并使用 Log Analytics 脚本 API 添加工作区。

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[!NOTE]
如果以前已使用命令行或脚本安装或配置代理，`EnableAzureOperationalInsights` 会被 `AddCloudWorkspace` 取代。

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>使用 Azure 自动化中的 DSC 安装代理

>[!NOTE]
此过程和脚本示例不会升级现有代理。

1. 将 [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 的 xPSDesiredStateConfiguration DSC 模块导入到 Azure 自动化。  
2.  为 *OPSINSIGHTS_WS_ID* 和 *OPSINSIGHTS_WS_KEY* 创建 Azure 自动化变量资产。 将 *OPSINSIGHTS_WS_ID* 设置为 OMS Log Analytics 工作区 ID，将 *OPSINSIGHTS_WS_KEY* 设置为工作区的主键。
3.  使用以下脚本并以 MMAgent.ps1 形式保存
4.  修改后参照以下示例使用 Azure 自动化中的 DSC 安装代理。 通过使用 Azure 自动化界面或 cmdlet 将 MMAgent.ps1 导入 Azure 自动化。
5.  向配置分配节点。 15 分钟内，该节点将检查其配置，MMA 将被推送到该节点。

```
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
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
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


## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>手动配置代理或添加额外的工作区
如果已安装代理但没有进行配置，或者希望代理向多个工作区报告，可使用以下信息来启用代理或重新配置代理。 配置代理后，会在代理服务中注册代理，并会获得必要的配置信息和包含解决方案信息的管理包。

1. 安装 Microsoft Monitoring Agent 后，打开“**控制面板**”。
2. 打开 **Microsoft Monitoring Agent**，然后单击“**Azure Log Analytics (OMS)** ”选项卡。   
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

## <a name="optionally-configure-agents-to-use-the-oms-gateway"></a>可选择将代理配置为使用 OMS 网关

如果有未连接到 Internet 的服务器或客户端，仍可以使用 OMS 网关让它们将数据发送到 OMS。  使用该网关时，会通过有权访问 Internet 的一台服务器发送来自代理的所有数据。 网关直接将数据从代理传输到 OMS，不对传输的任何数据进行分析。

请参阅 [OMS 网关](log-analytics-oms-gateway.md)了解有关网关的更多信息，包括安装和配置。

有关如何配置代理以使用代理服务器（本例中为 OMS 网关）的详细信息，请参阅[在 Log Analytics 中配置代理和防火墙设置](log-analytics-proxy-firewall.md)。

## <a name="optionally-configure-proxy-and-firewall-settings"></a>可选择配置代理服务器和防火墙设置
如果环境中有限制访问 Internet 的代理服务器或防火墙，请参阅“[在 Log Analytics 中配置代理服务器和防火墙设置](log-analytics-proxy-firewall.md)”以使代理可以与 OMS 服务通信。

## <a name="next-steps"></a>后续步骤

- [从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)，以添加功能和收集数据。
- 如果组织使用代理服务器或防火墙，请[在 Log Analytics 中配置代理服务器和防火墙设置](log-analytics-proxy-firewall.md)以便代理可以与 Log Analytics 服务进行通信。



<!--HONumber=Nov16_HO3-->


