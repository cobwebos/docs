---
title: 为 Azure AD 域服务启用安全审核 | Microsoft Docs
description: 了解如何在 Azure AD 域服务中启用安全审核来集中事件日志记录，以便进行分析和发出警报
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 98e64f1ea1196b6fd773514d4dad5b6e178a6898
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722528"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>为 Azure Active Directory 域服务启用安全审核

Azure Active Directory 域服务 (Azure AD DS) 安全审核允许 Azure 将安全事件流式传输到目标资源。 这些资源包括 Azure 存储、Azure Log Analytics 工作区或 Azure 事件中心。 启用安全审核事件后，Azure AD DS 会将所选类别的所有已审核事件发送到目标资源。

可以使用 Azure 事件中心将事件存档到 Azure 存储中，并将事件流式传输到安全信息和事件管理 (SIEM) 软件（或等效软件），也可以从 Azure 门户使用 Azure Log Analytics 工作区执行自己的分析。

> [!IMPORTANT]
> Azure AD DS 安全审核仅适用于基于 Azure 资源管理器的托管域。 有关如何迁移的信息，请参阅[将 Azure AD DS 从经典虚拟网络模型迁移到资源管理器][migrate-azure-adds]。

## <a name="security-audit-destinations"></a>安全审核目标

可以使用 Azure 存储、Azure 事件中心或 Azure Log Analytics 工作区作为 Azure AD DS 安全审核的目标资源。 这些目标可以组合在一起。 例如，可以使用 Azure 存储来存档安全审核事件，而使用 Azure Log Analytics 工作区来分析和报告短期内的信息。

下表概述了每种目标资源类型的场景。

> [!IMPORTANT]
> 在启用 Azure AD DS 安全审核之前，需要创建目标资源。 可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 创建这些资源。

| 目标资源 | 方案 |
|:---|:---|
|Azure 存储| 如果你的主要需求是出于存档目的存储安全审核事件，则应使用此目标。 其他目标可用于存档目的，但是这些目标提供的功能超出了存档的主要需求。 <br /><br />在启用 Azure AD DS 安全审核事件之前，首先[创建 Azure 存储帐户](../storage/common/storage-account-create.md)。|
|Azure 事件中心| 当主要需求是与其他软件（如数据分析软件或安全信息和事件管理 (SIEM) 软件）共享安全审核事件时，应使用此目标。<br /><br />在启用 Azure AD DS 安全审核事件之前，[使用 Azure 门户创建事件中心](../event-hubs/event-hubs-create.md)|
|Azure Log Analytics 工作区| 当你的主要需求是直接从 Azure 门户分析和查看安全审核时，应使用此目标。<br /><br />在启用 Azure AD DS 安全审核事件之前，[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>使用 Azure 门户启用安全审核事件

若要使用 Azure 门户启用 Azure AD DS 安全审核事件，请完成以下步骤。

> [!IMPORTANT]
> Azure AD DS 安全审核不可追溯。 无法检索或重播过去的事件。 Azure AD DS 只能发送启用安全审核之后发生的事件。

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 在 Azure 门户的顶部，搜索并选择“Azure AD 域服务”。 选择你的托管域，例如 *aaddscontoso.com*。
1. 在 Azure AD DS 窗口中，选择左侧的“诊断设置”。
1. 默认情况下未配置任何诊断。 若要开始，请选择“添加诊断设置”。

    ![为 Azure AD 域服务添加诊断设置](./media/security-audit-events/add-diagnostic-settings.png)

1. 输入诊断配置的名称，如 aadds-auditing。

    选中所需的安全审核目标框。 你可以从 Azure 存储帐户、Azure 事件中心或 Log Analytics 工作区中进行选择。 这些目标资源必须已存在于你的 Azure 订阅中。 不能在此向导中创建目标资源。

    ![启用所需的目标和审核事件类型以进行捕获](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure 存储**
        * 选择“存档到存储帐户”，然后选择“配置” 。
        * 选择要用于存档安全审核事件的“订阅”和“存储帐户” 。
        * 准备就绪后，选择“确定”。
    * **Azure 事件中心**
        * 选择“流式传输到事件中心”，然后选择“配置” 。
        * 选择“订阅”和“事件中心命名空间” 。 如果需要，还可以选择“事件中心名称”和“事件中心策略名称” 。
        * 准备就绪后，选择“确定”。
    * **Azure Log Analytic 工作区**
        * 选择“发送到 Log Analytics”，然后选择要用于存储安全审核事件的“订阅”和“Log Analytics 工作区”  。

1. 选择要为特定目标资源包含的日志类别。 如果将审核事件发送到 Azure 存储帐户，还可以配置保留策略来定义保留数据的天数。 默认设置为 0，指示保留所有数据，并且不会在一段时间后轮替事件。

    可以在单个配置中为每个目标资源选择不同的日志类别。 例如，此功能允许你选择要为 Log Analytics 保留的日志类别以及要存档的日志类别。

1. 完成后，选择“保存”以提交更改。 在保存配置后，目标资源会立即开始接收 Azure AD DS 安全审核事件。

## <a name="enable-security-audit-events-using-azure-powershell"></a>使用 Azure PowerShell 启用安全审核事件

若要使用 Azure PowerShell 启用 Azure AD DS 安全审核事件，请完成以下步骤。 如果需要，请先[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> Azure AD DS 安全审核不可追溯。 无法检索或重播过去的事件。 Azure AD DS 只能发送启用安全审核之后发生的事件。

1. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 对 Azure 订阅进行身份验证。 在系统提示时输入帐户凭据。

    ```azurepowershell
    Connect-AzAccount
    ```

1. 创建安全审核事件的目标资源。

    * **Azure 存储** - [使用 Azure PowerShell 创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure 事件中心** - [使用 Azure PowerShell 创建事件中心](../event-hubs/event-hubs-quickstart-powershell.md)。 你可能还需要使用 [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) cmdlet 来创建授权规则，该规则向事件中心命名空间授予 Azure AD DS 权限。 授权规则必须包括管理、侦听和发送权限  。

        > [!IMPORTANT]
        > 请确保对事件中心命名空间而不是对事件中心本身设置授权规则。

    * **Azure Log Analytics 工作区** - [使用 Azure PowerShell 创建 Log Analytics 工作区](../azure-monitor/platform/powershell-workspace-configuration.md)。

1. 使用 [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) cmdlet 获取 Azure AD DS 托管域的资源 ID。 创建名为 $aadds.ResourceId 的变量来保存值：

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. 使用 [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) cmdlet 配置 Azure 诊断设置，以使用 Azure AD 域服务安全审核事件的目标资源。 在下面的示例中，变量 $aadds.ResourceId 从上一步开始使用。

    * **Azure 存储** - 将 storageAccountId 替换为你的存储帐户名称：

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure 事件中心** - 将 eventHubName 替换为事件中心的名称，并将 eventHubRuleId 替换为授权规则 ID ：

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Analytics 工作区** - 将 workspaceId 替换为 Log Analytics 工作区的 ID：

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>使用 Azure Monitor 查询和查看安全审核事件

Log Analytic 工作区使你可以使用 Azure Monitor 和 Kusto 查询语言来查看和分析安全审核事件。 此查询语言专为只读使用而设计，它具有强大的分析功能和易于阅读的语法。 有关 Kusto 查询语言入门的详细信息，请参阅以下文章：

* [Azure Monitor 文档](../azure-monitor/index.yml)
* [Azure Monitor 中的 Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)
* [Azure Monitor 中的日志查询入门](../azure-monitor/log-query/get-started-queries.md)
* [创建和共享 Log Analytics 数据的仪表板](../azure-monitor/learn/tutorial-logs-dashboards.md)

下面的示例查询可用于从 Azure AD DS 开始分析安全审核事件。

### <a name="sample-query-1"></a>示例查询 1

查看过去 7 天的所有帐户锁定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>示例查询 2

查看 2020 年 6 月 3 日上午 9 点至 2020 年 6 月 10 日午夜 期间的所有帐户锁定事件 (4740)，按日期和时间升序排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>示例查询 3

查看名为 user 的帐户七天前（从现在算起）的登录事件：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>示例查询 4

查看尝试使用错误的密码登录的名为 user 的帐户七天前（从现在算起）的登录事件 (0xC0000006a)：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>示例查询 5

查看在帐户锁定时尝试登录的名为 user 的帐户七天前（从现在算起）的登录事件 (0xC0000234)：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>示例查询 6

查看所有锁定用户七天前（从现在算起）发生的所有登录尝试的帐户登录事件数：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>审核事件类别

Azure AD DS 安全审核与传统 AD DS 域控制器的传统审核一致。 在混合环境中，你可以重复使用现有审核模式，以便在分析事件时可以使用相同的逻辑。 根据要进行故障排除或分析的场景，需要定向不同的审核事件类别。

可以使用以下审核事件类别：

| 审核类别名称 | 说明 |
|:---|:---|
| 帐户登录|审核对域控制器或本地安全帐户管理器 (SAM) 上的帐户数据进行身份验证的尝试。</p>登录和注销策略设置和事件可跟踪访问特定计算机的尝试。 此类别中的设置和事件侧重于所使用的帐户数据库。 此类别包括以下子类别：<ul><li>[审核凭据验证](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[审核 Kerberos 身份验证服务](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[审核 Kerberos 服务票证操作](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[审核其他登录/注销事件](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 帐户管理|审核对用户和计算机帐户和组所做的更改。 此类别包括以下子类别：<ul><li>[审核应用程序组管理](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[审核计算机帐户管理](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[审核通讯组管理](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[审核其他帐户管理](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[审核安全组管理](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[审核用户帐户管理](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 详细信息跟踪|审核该计算机上的单个应用程序和用户的活动，并了解计算机的使用方式。 此类别包括以下子类别：<ul><li>[审核 DPAPI 活动](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[审核 PNP 活动](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[审核进程创建](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[审核进程终止](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[审核 RPC 事件](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 目录服务访问|审核访问和修改 Active Directory 域服务 (AD DS) 中的对象的尝试。 这些审核事件只记录在域控制器上。 此类别包括以下子类别：<ul><li>[审核详细目录服务复制](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[审核目录服务访问](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[审核目录服务更改](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[审核目录服务复制](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 登录-注销|审核以交互方式或通过网络登录到计算机的尝试。 这些事件对于跟踪用户活动和识别对网络资源的潜在攻击很有用。 此类别包括以下子类别：<ul><li>[审核帐户锁定](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[审核用户/设备声明](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[审核 IPsec 扩展模式](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[审核组成员身份](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[审核 IPsec 主模式](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[审核 IPsec 快速模式](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[审核注销](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[审核登录](/windows/security/threat-protection/auditing/audit-logon)</li><li>[审核网络策略服务器](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[审核其他登录/注销事件](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[审核特殊登录](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|对象访问| 审核访问网络或计算机上的特定对象或对象类型的尝试。 此类别包括以下子类别：<ul><li>[审核应用程序已生成](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[审核证书服务](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[审核详细文件共享](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[审核文件共享](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[审核文件系统](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[审核筛选平台连接](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[审核由筛选平台丢弃的数据包](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[审核句柄操作](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[审核内核对象](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[审核其他对象访问事件](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[审核注册表](/windows/security/threat-protection/auditing/audit-registry)</li><li>[审核可移动存储](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[审核 SAM](/windows/security/threat-protection/auditing/audit-sam)</li><li>[审核中心访问策略暂存](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|策略更改|审核对本地系统或网络上重要安全策略的更改。 策略通常由管理员建立，以帮助保护网络资源。 监视对这些策略的更改或监视更改这些策略的尝试可能是网络安全管理的一个重要方面。 此类别包括以下子类别：<ul><li>[审核审核策略更改](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[审核身份验证策略更改](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[审核授权策略更改](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[审核筛选平台策略更改](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[审核 MPSSVC 规则级别策略更改](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[审核其他策略更改](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|特权使用| 审核一个或多个系统上某些特权的使用。 此类别包括以下子类别：<ul><li>[审核非敏感特权的使用](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[审核敏感特权的使用](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[审核其他特权使用事件](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|系统| 审核对未包括在其他类别中且可能带来潜在安全隐患的计算机所做的系统级更改。 此类别包括以下子类别：<ul><li>[审核 IPsec 驱动程序](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[审核其他系统事件](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[审核安全状态更改](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[审核安全系统扩展](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[审核系统完整性](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>每个类别的事件 ID

 Azure AD DS 安全审核在特定操作触发可审核事件时记录以下事件 ID：

| 事件类别名称 | 事件 ID |
|:---|:---|
|帐户登录安全性|4767、4774、4775、4776、4777|
|帐户管理安全性|4720、4722、4723、4724、4725、4726、4727、4728、4729、4730、4731、4732、4733、4734、4735、4737、4738、4740、4741、4742、4743、4754、4755、4756、4757、4758、4764、4765、4766、4780、4781、4782、4793、4798、4799、5376、5377|
|详细信息跟踪安全性|无|
|DS 访问安全性|5136、5137、5138、5139、5141|
|登录-注销安全性|4624、4625、4634、4647、4648、4672、4675、4964|
|对象访问安全性|无|
|策略更改安全性|4670、4703、4704、4705、4706、4707、4713、4715、4716、4717、4718、4719、4739、4864、4865、4866、4867、4904、4906、4911、4912|
|特权使用安全性|4985|
|系统安全性|4612、4621|

## <a name="next-steps"></a>后续步骤

有关 Kusto 的特定信息，请参阅以下文章：

* Kusto 查询语言的[概述](/azure/kusto/query/)。
* [Kusto 教程](/azure/kusto/query/tutorial) 来熟悉查询基础知识。
* 帮助您了解查看数据的新方法的[示例查询](/azure/kusto/query/samples)。
* Kusto [最佳实践](/azure/kusto/query/best-practices) 以优化查询是否成功。

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md