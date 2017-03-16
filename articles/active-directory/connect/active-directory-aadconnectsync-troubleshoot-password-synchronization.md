---
title: "使用 Azure AD Connect 同步解决密码同步问题 | Microsoft Docs"
description: "提供有关如何解决密码同步问题的信息"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>使用 Azure AD Connect 同步解决密码同步问题
本主题提供有关如何解决密码同步问题的步骤。 如果密码未按预期同步，请区分该密码是一部分用户的密码还是所有用户的密码。

* 如果遇到密码都未同步的问题，请参阅[排查未同步任何密码的问题](#no-passwords-are-synchronized)。
* 如果单个对象出现问题，请参阅[排查一个对象的密码同步问题](#one-object-is-not-synchronizing-passwords)。

## <a name="no-passwords-are-synchronized"></a>未同步任何密码
按照下列步骤，了解为何未同步任何密码：

1. 连接服务器是否处于[暂存模式](active-directory-aadconnectsync-operations.md#staging-mode)？ 处于暂存模式的服务器不同步任何密码。
2. 运行[获取密码同步设置的状态](#get-the-status-of-password-sync-settings)部分中的脚本。 这样就可以获得密码同步配置的概述。  
![PowerShell 脚本从密码同步设置中返回的输出](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. 如果未在 Azure AD 中启用该功能，或者未启用同步通道状态，请运行 Connect 安装向导。 选择“自定义同步选项”并取消选择密码同步。 此项更改会暂时禁用该功能。 然后再次运行向导并重新启用密码同步。 再次运行脚本，验证配置是否正确。
4. 查看事件日志，查找错误。 查找以下事件，这些事件将指示问题：
    1. 源：“目录同步”ID：0、611、652、655 如果看到这些内容，则表示有连接问题。 事件日志消息包含林信息（其中有问题）。 有关详细信息，请参阅[连接问题](#connectivity problem)
5. 如果没有看到检测信号，或者其他方面均为异常，则运行[触发所有密码的完全同步](#trigger-a-full-sync-of-all-passwords)。 应仅运行此脚本一次。
6. 阅读[解决一个对象的密码同步问题](#one-object-is-not-synchronizing-passwords)部分。

### <a name="connectivity-problem"></a>连接问题

1. 是否与 Azure AD 连接？
2. 帐户是否具有读取所有域中的密码哈希的所需权限？ 如果已使用“快速”设置安装 Connect，则应已具有正确的权限。 如果使用自定义安装，则需要手动设置权限。
    1. 若要查找 Active Directory 连接器使用的帐户，请启动 **Synchronization Service Manager**。 转到“连接器”并查找正在排查的本地 Active Directory 林。 选择连接器，然后单击“属性”。 转到“连接到 Active Directory 林”。  
    ![AD 连接器使用的帐户](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    记下用户名和帐户所处的域。
    2. 启动“Active Directory 用户和计算机”。 验证以前步骤中的帐户在林中所有域的根目录设置了以下权限：
        * 复制目录更改
        * 复制所有目录更改
3. Azure AD Connect 是否可以访问域控制器？ 如果连接服务器无法连接到所有域控制器，则应配置“仅使用首选的域控制器”。  
    ![AD 连接器使用的域控制器](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    返回到“Synchronization Service Manager”和“配置目录分区”。 在“选择目录分区”中选择域，选中复选框“仅使用首选的域控制器”，然后单击“配置”。 在列表中，输入 Connect 应用于密码同步的域控制器。 同一列表也用于导入和导出。 对所有域执行这些步骤。
4. 如果脚本显示没有检测信号，请运行“触发所有密码的完全同步”[](#trigger-a-full-sync-of-all-passwords)中的脚本。

## <a name="one-object-is-not-synchronizing-passwords"></a>一个对象不同步密码
可以通过检查对象的状态，轻松排查密码同步问题。

1. 首先打开“Active Directory 用户和计算机”。 找到该用户并检查是否未选择“用户必须在下次登录时更改密码”。  
![Active Directory 效率密码](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
如果已选择，请要求该用户登录并更改密码。 临时密码不会同步到 Azure AD。
2. 如果 Active Directory 中的设置正确，下一步是在同步引擎中跟踪该用户。 从本地 Active Directory 到 Azure AD 的路径中跟踪该用户，可以查看该对象是否出现描述性错误。
    1. 启动**[同步服务管理器](active-directory-aadconnectsync-service-manager-ui.md)**。
    2. 单击“连接器”。
    3. 选择用户所在的 **Active Directory 连接器**。
    4. 选择“搜索连接器空间”。
    5. 在“作用域”中，选择“DN 或定位点”。 输入正在排查的用户的完整 DN。
    ![通过 DN 搜索 cs 中的用户](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. 找到正在查找的用户并单击“属性”以查看所有属性。 如果用户不在搜索结果中，则验证[筛选规则](active-directory-aadconnectsync-configure-filtering.md)，并确保运行[应用并验证更改](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes)以在 Connect 中显示用户。
    7. 若要查看对象在过去一周的密码同步详细信息，请单击“日志...”。  
    ![对象日志详细信息](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    如果对象日志为空，则 Azure AD Connect 不能从 Active Directory 读取密码哈希。 继续进行针对[连接错误](#connectivity-errors)的故障排除。 如果看到除“成功”外的任何其他值，则参阅[密码同步日志](#password-sync-log)中的表。
    8. 选择“沿袭”选项卡，确保至少有一个同步规则的“密码同步”显示为 **True**。 在默认配置中，同步规则的名称为 **In from AD - User AccountEnabled**。  
    ![有关用户的沿袭信息](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. 单击“Metaverse 对象属性”。 将在用户中看到属性的列表。  
    ![Metaverse 信息](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    验证属性 **cloudFiltered** 不存在。 确保域属性（domainFQDN 和 domainNetBios）具有所需值。
    10. 单击选项卡“连接器”。 确保看到连接到本地 AD 和 Azure AD 的连接器。
    ![Metaverse 信息](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. 选择表示 Azure AD 的行，然后单击“属性”。 单击选项卡“沿袭”。 连接器空间对象应存在一个“密码同步”设置为 **True** 的出站规则。 在默认配置中，同步规则的名称为 **Out to AAD - User Join**。  
    ![用户的连接器空间属性](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

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
> 应仅运行此脚本一次。 如果需要多次运行该脚本，则将出现其他问题。 请联系 Microsoft 支持，以帮助解决该问题。

可以使用以下脚本对所有密码触发完全同步：

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
* [使用 Azure AD Connect 同步实现密码同步](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Azure AD Connect Sync：自定义同步选项](active-directory-aadconnectsync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)

