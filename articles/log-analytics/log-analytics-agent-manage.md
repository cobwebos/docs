---
title: 管理 Azure Log Analytics 代理 | Microsoft Docs
description: 本文介绍在计算机上部署的 Microsoft Monitoring Agent (MMA) 的生命周期中通常会执行的不同管理任务。
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
ms.date: 03/30/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 908418dffaffc25be320bd0008edf03493aa4e55
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128786"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>管理并维护 Windows 和 Linux 的 Log Analytics 代理

为 Log Analytics 初始部署 Windows 或 Linux 代理后，可能需要重新配置代理，或者在代理到达生命周期中的停用阶段时将其从计算机中删除。  可以轻松地手动或自动管理这些日常维护任务，从而减少运行错误并降低费用。

## <a name="adding-or-removing-a-workspace"></a>添加或删除工作区 

### <a name="windows-agent"></a>Windows 代理

#### <a name="update-settings-from-control-panel"></a>从控制面板更新设置

1. 使用具有管理权限的帐户登录到计算机。
2. 打开“控制面板”
3. 选择“Microsoft Monitoring Agent”，然后单击“Azure Log Analytics (OMS)”选项卡。
4. 若要删除工作区，请选中该工作区，然后单击“删除”。 如果还需代理停止向任何其他工作区报告，请重复此步骤。
5. 若要添加工作区，请单击“添加”，然后在“添加 Log Analytics 工作区”对话框中粘贴工作区 ID 和工作区密钥（主密钥）。 如果计算机应向 Azure 政府云中的 Log Analytics 工作区报告，请从“Azure 云”下拉列表中选择“Azure 美国政府”。 
6. 单击“确定”  保存更改。

#### <a name="remove-a-workspace-using-powershell"></a>使用 PowerShell 删除工作区 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>使用 PowerShell 在 Azure 商业版中添加工作区 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>使用 PowerShell 在 Azure 美国政府版中添加工作区 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>如果以前已使用命令行或脚本安装或配置了代理，`EnableAzureOperationalInsights` 会被 `AddCloudWorkspace` 和 `RemoveCloudWorkspace` 取代。
>

### <a name="linux-agent"></a>Linux 代理
以下步骤演示如何重新配置 Linux 代理，以便将其注册到不同的工作区，或者从其配置中删除工作区。  

1.  若要验证该代理是否已注册到工作区，请运行以下命令。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    此命令应返回类似以下示例的状态 - 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    状态中还应显示代理正在运行，这一点非常重要，否则以下重新配置代理的步骤不会成功完成。  

2. 如果代理已注册到工作区，请运行以下命令删除已注册的工作区。  如果未注册，请继续执行下一步。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. 若要注册到不同的工作区，请运行命令 `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]` 
4. 若要验证更改是否生效，请运行以下命令。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    此命令应返回类似以下示例的状态 - 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

无需重启代理服务即可使更改生效。

## <a name="update-proxy-settings"></a>更新代理设置 
若要将代理配置为在部署后通过代理服务器或[ OMS 网关](log-analytics-oms-gateway.md)与服务通信，请使用以下任一方法来完成此任务。

### <a name="windows-agent"></a>Windows 代理

#### <a name="update-settings-using-control-panel"></a>使用控制面板更新设置

1. 使用具有管理权限的帐户登录到计算机。
2. 打开“控制面板”
3. 选择“Microsoft Monitoring Agent”，然后单击“代理设置”选项卡。
4. 单击“使用代理服务器”，然后提供代理服务器或网关的 URL 和端口号。 如果代理服务器或 OMS 网关要求身份验证，请键入用户名和密码进行身份验证，然后单击“确定”。 

#### <a name="update-settings-using-powershell"></a>使用 PowerShell 更新设置 

复制以下示例 PowerShell 代码，使用特定于自己环境的信息对其进行更新，并使用 PS1 文件扩展名将其保存。  在直接连接到 Log Analytics 服务的每台计算机上运行该脚本。

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

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
```  

### <a name="linux-agent"></a>Linux 代理
如果 Linux 计算机需要通过代理服务器或 OMS 网关与 Log Analytics 通信，请执行以下步骤。  代理配置值具有以下语法 `[protocol://][user:password@]proxyhost[:port]`。  *proxyhost* 属性接受代理服务器的完全限定域名或 IP 地址。

1. 运行以下命令编辑文件 `/etc/opt/microsoft/omsagent/proxy.conf`，并针对特定设置更改相关的值。

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. 运行以下命令重启代理： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>卸载代理
使用下列方式之一通过命令行或安装向导卸载 Windows 或 Linux 代理。

### <a name="windows-agent"></a>Windows 代理

#### <a name="uninstall-from-control-panel"></a>从控制面板卸载
1. 使用具有管理权限的帐户登录到计算机。  
2. 在“控制面板”中，单击“程序和功能”。
3. 在“程序和功能”中依次单击“Microsoft Monitoring Agent”、“卸载”、“是”。

>[!NOTE]
>还可以通过双击“MMASetup-<platform>.exe”运行代理安装向导，该程序可从 Azure 门户中的工作区下载。

#### <a name="uninstall-from-the-command-line"></a>从命令行卸载
下载的代理文件是使用 IExpress 创建的独立安装包。  代理和支持文件的安装程序包含在该包中，需要提取才能使用以下示例中所示的命令行正确卸载。 

1. 使用具有管理权限的帐户登录到计算机。  
2. 若要提取代理安装文件，请在提升的命令提示符处运行 `extract MMASetup-<platform>.exe`，这会提示要将文件提取到的路径。  或者，可以通过传递参数 `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` 来指定路径。  若要深入了解 IExpress 支持的命令行开关，请参阅 [IExpress 的命令行开关](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)，然后根据需要更新示例。
3. 在提示符下键入 `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`。  

### <a name="linux-agent"></a>Linux 代理
若要删除代理，请在 Linux 计算机上运行以下命令。  *--purge* 参数可彻底删除代理及其配置。

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>将代理配置为向 Operations Manager 管理组报告

### <a name="windows-agent"></a>Windows 代理
执行以下步骤可将适用于 Windows 的 OMS 代理配置为向 System Center Operations Manager 管理组报告。 

1. 使用具有管理权限的帐户登录到计算机。
2. 打开“控制面板” 
3. 单击“Microsoft Monitoring Agent”，然后单击“Operations Manager”选项卡。
4. 如果 Operations Manager 服务器与 Active Directory 集成，请单击“**自动更新 AD DS 中的管理组分配**”。
5. 单击“**添加**”以打开“**添加管理组**”对话框。
6. 在“管理组名称”字段，键入管理组的名称。
7. 在“主管理服务器”字段，键入主管理服务器的计算机名。
8. 在“管理服务器端口”字段，键入 TCP 端口号。
9. 在“**代理操作帐户**”下面，选择本地系统帐户或本地域帐户。
10. 单击“**确定**”关闭“**添加管理组**”对话框中，再单击“**确定**”关闭“**Microsoft Monitoring Agent 属性**”对话框。

### <a name="linux-agent"></a>Linux 代理
执行以下步骤可将 OMS Agent for Linux 配置为向 System Center Operations Manager 管理组报告。 

1. 编辑 `/etc/opt/omi/conf/omiserver.conf` 文件
2. 确保以 `httpsport=` 开头的行定义了端口 1270。 例如：`httpsport=1270`
3. 重新启动 OMI 服务器：`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>后续步骤

如果在安装或管理代理时遇到问题，请查看 [Linux 代理疑难解答](log-analytics-agent-linux-support.md)。  