---
title: 使用 Azure AD Connect 同步解决密码哈希同步问题 | Microsoft Docs
description: 本文提供有关如何排查密码哈希同步问题的信息。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: billmath
ms.openlocfilehash: bcf266813476c682d47bfd483db77f5d8b73837a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>使用 Azure AD Connect 同步解决密码哈希同步问题
本主题提供解决密码哈希同步问题的步骤。 如果密码未按预期同步，请区分该密码是一部分用户的密码还是所有用户的密码。

对于 1.1.614.0 版或更高版本的 Azure Active Directory (Azure AD) Connect 部署，使用向导中的故障排除任务来排除密码哈希同步问题：

* 如果遇到未同步任何密码的问题，请参阅[未同步任何密码：使用故障排除任务进行故障排除](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task)部分。

* 如果个别对象出现问题，请参阅[一个对象未同步密码：使用故障排除任务进行故障排除](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task)部分。

对于 1.1.524.0 版或更高版本的部署，可以使用一个诊断 cmdlet 排查密码哈希同步问题：

* 如果遇到未同步任何密码的问题，请参阅[未同步任何密码：使用诊断 cmdlet 排查问题](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)部分。

* 如果单个对象出现问题，请参阅[一个对象未同步密码：使用诊断 cmdlet 排查问题](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet)部分。

对于较早版本的 Azure AD Connect 部署：

* 如果遇到未同步任何密码的问题，请参阅[未同步任何密码：手动排查问题的步骤](#no-passwords-are-synchronized-manual-troubleshooting-steps)部分。

* 如果单个对象出现问题，请参阅[一个对象未同步密码：手动排查问题的步骤](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps)部分。



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>未同步任何密码：使用故障排除任务进行故障排除
可以使用此故障排除任务查明未同步任何密码的原因。

> [!NOTE]
> 此故障排除任务仅可用于 Azure AD Connect 1.1.614.0 版或更高版本。

### <a name="run-the-troubleshooting-task"></a>运行故障排除任务
排查未同步任何密码的问题：

1. 使用“以管理员身份运行”选项，在 Azure AD Connect 服务器上打开一个新的 Windows PowerShell 会话。

2. 运行 `Set-ExecutionPolicy RemoteSigned` 或 `Set-ExecutionPolicy Unrestricted`。

3. 启动 Azure AD Connect 向导。

4. 导航到“其他任务”页面，选择“故障排除”，然后单击“下一步”。

5. 在“故障排除”页上，单击“启动”以在 PowerShell 中启动故障排除菜单。

6. 在主菜单中，选择“排查密码哈希同步问题”。

7. 在子菜单中，选择“密码哈希同步根本不工作”。

### <a name="understand-the-results-of-the-troubleshooting-task"></a>了解故障排除任务的结果
此故障排除任务执行以下检查：

* 验证是否为 Azure AD 租户启用了密码哈希同步功能。

* 验证 Azure AD Connect 服务器是否未处于暂存模式。

* 对于每个现有本地 Active Directory 连接器（对应于现有 Active Directory 林）：

   * 验证是否启用了密码哈希同步功能。
   
   * 在 Windows 应用程序事件日志中搜索密码哈希同步检测信号事件。

   * 对于本地 Active Directory 连接器下的每个 Active Directory 域：

      * 验证是否可从 Azure AD Connect 服务器访问该域。

      * 验证本地 Active Directory 连接器所用的 Active Directory 域服务 (AD DS) 帐户是否具有正确的用户名、密码和密码哈希同步所需的权限。

下图演示了对单个域的本地 Active Directory 拓扑运行 cmdlet 的结果：

![密码哈希同步的诊断输出](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalgeneral.png)

本部分的剩余内容说明了此任务返回的具体结果以及相应问题。

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>未启用密码哈希同步功能
如果尚未通过 Azure AD Connect 向导启用密码哈希同步，则返回以下错误：

![未启用密码哈希同步](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect 服务器处于暂存模式
如果 Azure AD Connect 服务器处于暂存模式，则暂时禁用密码哈希同步，并返回以下错误：

![Azure AD Connect 服务器处于暂存模式](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>没有密码哈希同步检测信号事件
每个本地 Active Directory 连接器都有其自己的密码哈希同步通道。 如果已创建密码哈希同步通道，并且没有任何要同步的密码更改，Windows 应用程序事件日志中将每隔 30 分钟生成一次检测信号事件 (EventId 654)。 对于每个本地 Active Directory 连接器，cmdlet 将搜索过去三小时内相应的检测信号事件。 如果找不到检测信号事件，则返回以下错误：

![没有密码哈希同步检测信号事件](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS 帐户没有正确的权限
如果本地 Active Directory 连接器用于同步密码哈希的 AD DS 帐户没有相应的权限，则返回以下错误：

![错误的凭据](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>错误的 AD DS 帐户用户名或密码
如果本地 Active Directory 连接器用于同步密码哈希的 AD DS 帐户的用户名或密码不正确，则返回以下错误：

![错误的凭据](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>一个对象未同步密码：使用故障排除任务进行故障排除

可以使用此故障排除任务来确定某个对象未同步密码的原因。

> [!NOTE]
> 此故障排除任务仅可用于 Azure AD Connect 1.1.614.0 版或更高版本。

### <a name="run-the-diagnostics-cmdlet"></a>运行诊断 cmdlet
排查特定用户对象的问题：

1. 使用“以管理员身份运行”选项，在 Azure AD Connect 服务器上打开一个新的 Windows PowerShell 会话。

2. 运行 `Set-ExecutionPolicy RemoteSigned` 或 `Set-ExecutionPolicy Unrestricted`。

3. 启动 Azure AD Connect 向导。

4. 导航到“其他任务”页面，选择“故障排除”，然后单击“下一步”。

5. 在“故障排除”页上，单击“启动”以在 PowerShell 中启动故障排除菜单。

6. 在主菜单中，选择“排查密码哈希同步问题”。

7. 在子菜单中，选择“特定用户帐户的密码未同步”。

### <a name="understand-the-results-of-the-troubleshooting-task"></a>了解故障排除任务的结果
此故障排除任务执行以下检查：

* 检查 Active Directory 连接器空间中的 Active Directory 对象、Metaverse 和 Azure AD 连接器空间的状态。

* 验证是否为密码哈希同步启用了同步规则，并将该规则应用于 Active Directory 对象。

* 尝试检索和显示上次尝试同步对象密码的结果。

下图演示了使用 cmdlet 排查单个对象的密码哈希同步问题的结果：

![密码哈希同步的诊断输出 - 单个对象](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectgeneral.png)

本部分的剩余内容说明了 cmdlet 返回的特定结果以及相应问题。

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory 对象未导出到 Azure AD
此本地 Active Directory 帐户的密码哈希同步失败。因为 Azure AD 租户中没有相应的对象。 返回以下错误：

![缺少 Azure AD 对象](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>用户拥有临时密码
当前，Azure AD Connect 不支持同步 Azure AD 的临时密码。 如果在本地 Active Directory 用户中设置了“下次登录时更改密码”选项，则会将密码视为临时密码。 返回以下错误：

![未导出临时密码](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>上次尝试同步密码的结果不可用
默认情况下，Azure AD Connect 会将密码哈希同步尝试的结果存储七天。 如果所选 Active Directory 对象没有可用结果，则返回以下警告：

![单个对象的诊断输出 - 没有密码同步历史记录](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>未同步任何密码：使用诊断 cmdlet 排查问题
可以使用 `Invoke-ADSyncDiagnostics` cmdlet 找出未同步任何密码的原因。

> [!NOTE]
> `Invoke-ADSyncDiagnostics` cmdlet 仅可用于 Azure AD Connect 1.1.524.0 版或更高版本。

### <a name="run-the-diagnostics-cmdlet"></a>运行诊断 cmdlet
排查未同步任何密码的问题：

1. 使用“以管理员身份运行”选项，在 Azure AD Connect 服务器上打开一个新的 Windows PowerShell 会话。

2. 运行 `Set-ExecutionPolicy RemoteSigned` 或 `Set-ExecutionPolicy Unrestricted`。

3. 运行 `Import-Module ADSyncDiagnostics`。

4. 运行 `Invoke-ADSyncDiagnostics -PasswordSync`。



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>一个对象未同步密码：使用诊断 cmdlet 排查问题
可使用 `Invoke-ADSyncDiagnostics` cmdlet 来确定某个对象未同步密码的原因。

> [!NOTE]
> `Invoke-ADSyncDiagnostics` cmdlet 仅可用于 Azure AD Connect 1.1.524.0 版或更高版本。

### <a name="run-the-diagnostics-cmdlet"></a>运行诊断 cmdlet
排查没有为用户同步任何密码的问题：

1. 使用“以管理员身份运行”选项，在 Azure AD Connect 服务器上打开一个新的 Windows PowerShell 会话。

2. 运行 `Set-ExecutionPolicy RemoteSigned` 或 `Set-ExecutionPolicy Unrestricted`。

3. 运行 `Import-Module ADSyncDiagnostics`。

4. 运行以下 cmdlet：
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   例如：
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>未同步任何密码：手动排查问题的步骤
按照下列步骤确定未同步任何密码的原因：

1. 连接服务器是否处于[暂存模式](active-directory-aadconnectsync-operations.md#staging-mode)？ 处于暂存模式的服务器不同步任何密码。

2. 运行[获取密码同步设置的状态](#get-the-status-of-password-sync-settings)部分中的脚本。 这样就可以获得密码同步配置的概述。  

    ![PowerShell 脚本从密码同步设置中返回的输出](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/psverifyconfig.png)  

3. 如果未在 Azure AD 中启用该功能，或者未启用同步通道状态，请运行 Connect 安装向导。 选择“自定义同步选项”并取消选择密码同步。此项更改会暂时禁用该功能。 然后再次运行向导并重新启用密码同步。再次运行脚本，验证配置是否正确。

4. 查看事件日志，查找错误。 查找以下事件，这些事件将指示问题：
    * 源：“目录同步”ID：0、611、652、655 如果看到这些事件，则表示有连接问题。 事件日志消息包含有问题的林信息。 有关详细信息，请参阅[连接问题](#connectivity problem)。

5. 如果没有看到检测信号，或者其他方面均为异常，则运行[触发所有密码的完全同步](#trigger-a-full-sync-of-all-passwords)。 仅运行该脚本一次。

6. 请参阅[排查一个对象未同步密码的问题](#one-object-is-not-synchronizing-passwords)部分。

### <a name="connectivity-problems"></a>连接问题

是否与 Azure AD 连接？

帐户是否具有读取所有域中的密码哈希的所需权限？ 如果已使用“快速”设置安装 Connect，则应已具有正确的权限。 

如果使用自定义安装，请按照以下步骤手动设置权限：
    
1. 若要查找 Active Directory 连接器使用的帐户，请启动 Synchronization Service Manager。 
 
2. 转到“连接器”，并搜索正在排查的本地 Active Directory 林。 
 
3. 选择连接器，然后单击“属性”。 
 
4. 转到“连接到 Active Directory 林”。  
    
    ![Active Directory 连接器使用的帐户](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/connectoraccount.png)  
    记下用户名和帐户所处的域。
    
5. 打开“Active Directory 用户和计算机”，然后验证之前找到的帐户是否在林中所有域的根目录中设置了以下权限：
    * 复制目录更改
    * 复制所有目录更改

6. Azure AD Connect 是否可以访问域控制器？ 如果 Connect 服务器无法连接到所有域控制器，请配置“仅使用首选的域控制器”。  
    
    ![Active Directory 连接器使用的域控制器](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/preferreddc.png)  
    
7. 返回到“Synchronization Service Manager”和“配置目录分区”。 
 
8. 在“选择目录分区”中选择域，选中“仅使用首选的域控制器”复选框，然后单击“配置”。 

9. 在列表中，输入应由 Connect 用于密码同步的域控制器。同一列表也用于导入和导出。 对所有域执行这些步骤。

10. 如果脚本显示没有检测信号，请运行“触发所有密码的完全同步”中的脚本[](#trigger-a-full-sync-of-all-passwords)。

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>一个对象未同步密码：手动排查问题的步骤
可以通过检查对象的状态，轻松排查密码哈希同步问题。

1. 在“Active Directory 用户和计算机”中搜索用户，然后验证是否清除了“用户必须在下次登录时更改密码”复选框。  

    ![Active Directory 效率密码](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/adprodpassword.png)  

    如果选中该复选框，则要求用户登录并更改密码。 临时密码不会与 Azure AD 同步。

2. 如果 Active Directory 中的密码正确，请在同步引擎中跟踪该用户。 在从本地 Active Directory 到 Azure AD 的路径中跟踪该用户，可以查看该对象是否出现描述性错误。

    a. 启动 [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)。

    b. 单击“连接器”。

    c. 选择用户所在的 Active Directory 连接器。

    d.单击“下一步”。 选择“搜索连接器空间”。

    e. 在“作用域”框中，选择“DN 或定位点”，然后输入要排查的用户的完整 DN。

    ![在连接器空间中使用 DN 搜索用户](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/searchcs.png)  

    f. 找到正在查找的用户，然后单击“属性”查看所有特性。 如果用户不在搜索结果中，请验证[筛选规则](active-directory-aadconnectsync-configure-filtering.md)，并确保运行[应用并验证更改](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes)以在 Connect 中显示用户。

    g. 若要查看对象在过去一周的密码同步详细信息，请单击“日志”。  

    ![对象日志详细信息](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/csobjectlog.png)  

    如果对象日志为空，则 Azure AD Connect 无法从 Active Directory 读取密码哈希。 继续进行针对[连接错误](#connectivity-errors)的故障排除。 如果看到除“成功”外的任何其他值，请参阅[密码同步日志](#password-sync-log)中的表。

    h. 选择“沿袭”选项卡，确保至少有一个同步规则的“密码同步”列设置为“True”。 在默认配置中，同步规则的名称为“In from AD - User AccountEnabled”。  

    ![有关用户的沿袭信息](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync.png)  

    i. 单击“Metaverse 对象属性”，显示用户特性列表。  

    ![Metaverse 信息](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvpasswordsync.png)  

    验证 cloudFiltered 属性不存在。 确保域属性（domainFQDN 和 domainNetBios）具有所需值。

    j. 单击“连接器”选项卡。请确保同时看到本地 Active Directory 和 Azure AD 的连接器。

    ![Metaverse 信息](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvconnectors.png)  

    k. 选择表示 Azure AD 的行，单击“属性”，然后单击“沿袭”选项卡。连接器空间对象应存在一个“密码同步”列设置为“True”的出站规则。 在默认配置中，同步规则的名称为 **Out to AAD - User Join**。  

    ![连接器空间对象属性对话框](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>密码同步日志
状态列可能包含以下值：

| 状态 | 说明 |
| --- | --- |
| 成功 |已成功同步密码。 |
| FilteredByTarget |密码设置为“用户在下次登录时必须更改密码”。 未同步密码。 |
| NoTargetConnection |Metaverse 或 Azure AD 连接器空间中没有任何对象。 |
| SourceConnectorNotPresent |在本地 Active Directory 连接器空间中找不到任何对象。 |
| TargetNotExportedToDirectory |尚未导出 Azure AD 连接器空间中的对象。 |
| MigratedCheckDetailsForMoreInfo |日志条目创建于版本 1.0.9125.0 之前，并且以其旧状态显示。 |
| 错误 |服务返回未知错误。 |
| Unknown |尝试处理一批密码哈希时出错。  |
| MissingAttribute |Azure AD 域服务所需的特定属性（如 Kerberos 哈希）不可用。 |
| RetryRequestedByTarget |Azure AD 域服务所需的特定属性（如 Kerberos 哈希）以前不可用。 尝试重新同步用户的密码哈希。 |

## <a name="scripts-to-help-troubleshooting"></a>有助于故障排除的脚本

### <a name="get-the-status-of-password-sync-settings"></a>获取密码同步设置的状态
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>触发所有密码的完全同步
> [!NOTE]
> 仅运行此脚本一次。 如果需要多次运行该脚本，会出现其他问题。 要排查问题，请联系 Microsoft 支持部门。

可以使用以下脚本触发所有密码的完全同步：

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>后续步骤
* [使用 Azure AD Connect 同步实现密码哈希同步](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
* [Azure AD Connect 同步：自定义同步选项](active-directory-aadconnectsync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
