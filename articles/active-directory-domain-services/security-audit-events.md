---
title: 为 Azure AD 域服务启用安全审核 |Microsoft Docs
description: 为 Azure AD 域服务中启用安全审核
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566490"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>为 Azure AD 域服务 （预览版） 启用安全审核
Azure AD 域服务安全审核可让客户使用 Azure AD 域服务门户安全审核事件流式传输到目标资源。 可以接收这些事件的资源包括 Azure 存储、 Azure Log Analytics 工作区或 Azure 事件中心。 不久前才启用安全审核事件，Azure AD 域服务将所选类别的所有已审核的事件发送到目标资源。 安全审核事件启用到 Azure 存储中存档审核事件的客户。 此外，客户可以事件流式传输到安全信息和事件管理 (SIEM) 软件 （或等效） 使用事件中心，或执行其自己的分析和见解从 Azure 门户使用 Azure Log Analytics。 

> [!IMPORTANT]
> Azure AD 域服务安全审核是仅在基于 Azure 资源管理器的 Azure AD 域服务的实例上可用。
>
>

## <a name="auditing-event-categories"></a>审核事件类别
Azure AD 域服务安全审核功能与传统审核时，如果 Active Directory 域服务域控制器保持一致。 重复使用现有的审核模式可确保分析事件时，可能使用相同的逻辑。 Azure AD 域服务安全审核功能包括以下事件类别。

| 审核类别名称 | 描述 |
|:---|:---|
| 帐户登录|审核尝试进行身份验证的域控制器上或在本地安全帐户管理器 (SAM) 帐户数据。</p>登录和注销策略设置和事件跟踪尝试访问特定的计算机。 设置和类别关注于使用的帐户数据库中的事件。 此类别包括以下子类别：<ul><li>[审核凭据验证](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[审核 Kerberos 身份验证服务](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[审核 Kerberos 服务票证操作](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[审核其他登录/注销事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 帐户管理|审核更改为用户和计算机帐户和组。 此类别包括以下子类别：<ul><li>[审核应用程序组管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[审核计算机帐户管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[审核分发组管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[审核其他帐户管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[审核安全组管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[审核用户帐户管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 跟踪的详细信息|单个应用程序和用户在该计算机上，并了解如何使用一台计算机的审核活动。 此类别包括以下子类别：<ul><li>[审核 DPAPI 活动](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[即插即用的审核活动](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[审核进程创建](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[审核进程终止](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[审核 RPC 事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 目录服务访问|审核尝试访问和修改 Active Directory 域服务 (AD DS) 中的对象。 这些审核事件记录仅在域控制器。 此类别包括以下子类别：<ul><li>[审核详细的目录服务复制](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[审核目录服务访问](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[审核目录服务更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[审核目录服务复制](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 登录-注销|审核尝试以交互方式或通过网络登录到计算机。 这些事件可用于跟踪用户活动和识别潜在的攻击对网络资源。 此类别包括以下子类别：<ul><li>[审核帐户锁定](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[审核用户/设备声明](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[审核 IPsec 扩展模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[审核组成员身份](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[审核 IPsec 主模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[审核 IPsec 快速模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[审核注销](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[审核登录](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[审核网络策略服务器](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[审核其他登录/注销事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[审核特殊登录](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|对象访问| 审核尝试访问特定对象或类型的网络或计算机上的对象。 此类别包括以下子类别：<ul><li>[审核生成的应用程序](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[审核证书服务](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[审核详细的文件共享](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[审核文件共享](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[审核文件系统](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[审核筛选平台连接](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[审核筛选平台数据包丢弃](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[审核句柄操作](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[审核内核对象](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[审核其他对象访问事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[审核注册表](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[审核可移动存储](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Audit SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[审核中心访问策略暂存](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|策略更改|审核将更改为本地系统或网络上的重要的安全策略。 策略通常是由管理员来帮助安全的网络资源建立的。 监视更改或尝试更改这些策略可以是网络的安全管理的一个重要方面。 此类别包括以下子类别：<ul><li>[审核审核策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[审核身份验证策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[审核授权策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[审核筛选平台策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[审核 MPSSVC 规则级别策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[审核其他策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|权限使用| 审核某些权限在一个或多个系统上的使用。 此类别包括以下子类别：<ul><li>[审核非敏感权限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[审核敏感权限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[审核其他权限使用事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|系统| 审核系统级别更改不包括在其他类别和的计算机具有潜在的安全意义。 此类别包括以下子类别：<ul><li>[审核 IPsec 驱动程序](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[审核其他系统事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[审核安全状态更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[审核安全系统扩展](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[审核系统完整性](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>每个类别的事件 Id
 特定的操作触发的可审核事件时，azure AD 域服务安全审核记录以下事件 Id。

| 事件类别名称 | 事件 ID |
|:---|:---|
|帐户的登录安全性|4767, 4774, 4775, 4776, 4777|
|帐户管理安全性|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|详细信息跟踪安全|无|
|DS 访问安全性|5136, 5137, 5138, 5139, 5141|
|登录-注销安全|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|对象访问安全性|无|
|策略更改的安全性|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|特权使用安全性|4985|
|系统安全|4612, 4621|

## <a name="enable-security-audit-events"></a>启用安全审核事件
以下指南可帮助您成功订阅 Azure AD 域服务安全审核事件。

> [!IMPORTANT]
> Azure AD 域服务安全审核是不可撤消的。 不能检索从过去的事件，或若要重播从过去的事件。 该服务只能发送事件发生后启用它。
>

### <a name="choose-the-target-resource"></a>选择目标资源
目标资源提供 Azure 存储、 Azure 事件中心或 Azure Log Analytics 工作区的任意组合可用于安全审核。 请考虑下表中的用例的最佳资源。

> [!IMPORTANT]
> 需要启用 Azure AD 域服务安全审核之前创建的目标资源。
>

| 目标资源 | 场景 |
|:---|:---|
|Azure 存储|请考虑使用此目标时主需要是出于存档目的存储的安全审核事件。 其他目标可用于存档目的;但是，这些目标提供超出存档的主要需求的功能。 若要创建 Azure 存储帐户，请遵循[创建存储帐户。](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure 事件中心|请考虑使用此目标时主需要是共享其他软件，如数据分析软件或安全信息和事件管理 (SIEM) 软件的安全审核事件。 若要创建事件中心，请遵循[快速入门：创建事件中心使用 Azure 门户。](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics 工作区|请考虑使用此目标时主需要是分析，并直接查看从 Azure 门户的安全审核。  若要创建 Log Analytics 工作区，请按照[在 Azure 门户中创建 Log Analytics 工作区。](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>使用 Azure 门户启用安全审核事件 
1. 通过 https://portal.azure.com 登录到 Azure 门户。  在 Azure 门户中，单击所有服务。 在资源列表中，键入**域**。 开始键入时，会根据输入筛选该列表。 单击**Azure AD 域服务**。
2. 单击从列表中的 Azure AD 域服务实例。
3. 单击**诊断设置 （预览）** 从左侧上的操作列表。</p>
![诊断设置操作](./media/security-audit-events/diagnostic-settings-action.png)
4. 键入诊断配置的名称 (**aadds 审核**作为示例)。</p>
![诊断设置页](./media/security-audit-events/diagnostic-settings-page.png)
5. 选择相应的复选框将使用具有安全审核事件的目标资源旁边。
    > [!NOTE]
    > 不能在此页创建目标资源。
    >
    
    **azure 存储：**</p>
    选择**存档到存储帐户**。 单击 **“配置”** 。 选择**订阅**并**存储帐户**你想要使用存档安全审核事件。 单击“确定”。 </p>
    
    ![诊断存储设置](./media/security-audit-events/diag-settings-storage.png)
    
    **Azure 事件中心：**</p>
    选择**到事件中心的 Stream**。 单击 **“配置”** 。 在中**选择事件中心页**，选择**订阅**用于创建事件中心。 接下来，选择**事件中心命名空间**，**事件中心名称**，并**事件中心策略名称**。 单击“确定”。 </p>
    ![诊断事件中心设置](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Azure 日志分析工作区：**</p>
    选择“发送到 Log Analytics”  。 选择**订阅**并**Log Analytics 工作区**用于存储安全审核事件。</p>
    ![诊断工作区设置](./media/security-audit-events/diag-settings-log-analytics.png)

6. 选择要包含的特定目标资源的日志类别。 如果使用的存储帐户，可以配置保留策略。

    > [!NOTE]
    > 可以选择对单个配置每个目标资源的不同的日志类别。 这使你可以选择该记录你想要 Log Analytics 和其日志类别保留你想要存档的类别。
    >

7. 单击**保存**提交所做的更改。 目标资源会收到 Azure AD 域服务安全审核事件保存配置后不久。

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>使用 Azure PowerShell 启用安全审核事件
 
### <a name="prerequisites"></a>必备组件

请按照文章中的说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="enable-security-audits"></a>启用安全审核

1. 进行到 Azure 资源管理器的相应租户和订阅使用的身份验证**Connect AzAccount** Azure PowerShell cmdlet。
2. 创建目标资源的安全审核事件。</p>
    **azure 存储：**</p>
    请按照[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell)创建你的存储帐户。</p>
    **Azure 事件中心：**</p>
    请按照[快速入门：创建事件中心使用 Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell)创建事件中心。 您可能还需要使用[新建 AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) Azure PowerShell cmdlet 来创建授权规则以允许到事件中心的 Active Directory AD 域服务权限**命名空间**. 必须包括授权规则**管理**，**侦听**，并**发送**权限。
    > [!IMPORTANT]
    > 请确保事件中心命名空间和不是事件中心上设置授权规则。
       
    </p>
    
    **Azure 日志分析工作区：**</p>
    请按照[使用 Azure PowerShell 创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh)创建你的工作区。
3. 获取 Azure AD 域服务实例的资源 ID。 在打开、 已经过身份验证 Windows PowerShell 控制台中，键入以下命令。 使用 **$aadds。ResourceId**变量作为未来 cmdlet 的 Azure AD 域服务资源 ID 的参数。
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. 使用**集 AzDiagnosticSetting** cmdlet 来配置要使用的 Azure AD 域服务安全审核事件的目标资源的 Azure 诊断设置。 在以下示例中，变量 $aadds。资源 Id 表示 Azure AD 域服务实例的资源 ID （请参阅步骤 3）。</p>
    **azure 存储：**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    替换*storageAccountId*与你的存储帐户 id。</p>
    
    **Azure 事件中心：**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    替换*eventHubName*与事件中心的名称。 替换*eventHubRuleId*您以前创建的授权规则 id。</p>
    
    **Azure 日志分析工作区：**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    替换*workspaceId*以前创建的 Log Analytics 工作区的 id。 

## <a name="view-security-audit-events-using-azure-monitor"></a>使用 Azure Monitor 查看安全审核事件
日志分析工作区，可以查看和分析使用 Azure Monitor 和 Kusto 查询语言的安全审核事件。 查询语言用于只读方面做出了电源使用易于理解语法的分析功能的使用。
以下是一些资源，以帮助你开始使用 Kusto 查询语言。
* [Azure Monitor 文档](https://docs.microsoft.com/azure/azure-monitor/)
* [开始使用 Azure Monitor 中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [开始使用 Azure Monitor 中的日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [创建和共享 Log Analytics 数据的仪表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>示例查询

### <a name="sample-query-1"></a>示例查询 1
帐户锁定的所有事件在过去七天。
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>示例查询 2
之间的所有帐户锁定事件 (4740) 2019 年 6 月 26 日上午 9 点 2019 年 7 月 1 日午夜和，排序升序按日期和时间。
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>示例查询 3
上的帐户日志事件前七天 （从现在） 的用户帐户。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>示例查询 4
帐户登录事件七天前从现在为帐户名为尝试使用密码不正确 (0xC0000006a) 进行登录的用户。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>示例查询 5
帐户登录事件七天前从现在为帐户名为尝试登录，而该帐户已锁定 (0xC0000234) 的用户。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>示例查询 6
帐户登录事件数七天前从现在的所有登录尝试发生的所有用户已锁定。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>相关内容
* [概述](https://docs.microsoft.com/azure/kusto/query/)的 Kusto 查询语言。
* [Kusto 教程](https://docs.microsoft.com/azure/kusto/query/tutorial)可帮助你熟悉查询基础知识。
* [示例查询](https://docs.microsoft.com/azure/kusto/query/samples)，可以帮助您了解如何查看数据的新方式。
* Kusto[最佳做法](https://docs.microsoft.com/azure/kusto/query/best-practices)– 优化查询以便成功。














 
