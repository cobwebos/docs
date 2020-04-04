---
title: 为 Azure AD 域服务启用安全审核 |微软文档
description: 了解如何启用安全审核以集中记录 Azure AD 域服务中的分析和警报事件
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ce910b553e14d09eefa35efc5f2973337dfa1309
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654661"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>为 Azure 活动目录域服务启用安全审核

Azure 活动目录域服务 （Azure AD DS） 安全审核允许 Azure 将安全事件流式传输到目标资源。 这些资源包括 Azure 存储、Azure 日志分析工作区或 Azure 事件中心。 启用安全审核事件后，Azure AD DS 会将所选类别的所有审核事件发送到目标资源。

您可以使用 Azure 事件中心将事件存档到 Azure 存储中，并将事件流式传输到安全信息和事件管理 （SIEM） 软件（或等效软件），或者执行自己的分析和使用 Azure 门户中的 Azure 日志分析工作区。

> [!IMPORTANT]
> Azure AD DS 安全审核仅适用于基于 Azure 资源管理器的实例。 有关如何迁移的信息，请参阅[将 Azure AD DS 从经典虚拟网络模型迁移到资源管理器][migrate-azure-adds]。

## <a name="security-audit-destinations"></a>安全审核目标

可以将 Azure 存储、Azure 事件中心或 Azure 日志分析工作区用作 Azure AD DS 安全审核的目标资源。 这些目的地可以组合。 例如，可以使用 Azure 存储存档安全审核事件，但使用 Azure 日志分析工作区来分析和报告信息。

下表概述了每种目标资源类型的方案。

> [!IMPORTANT]
> 在启用 Azure AD DS 安全审核之前，需要创建目标资源。 可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 创建这些资源。

| 目标资源 | 方案 |
|:---|:---|
|Azure 存储| 当您的主要需要是存储安全审核事件以用于存档时，应使用此目标。 其他目标可用于存档目的，但这些目标提供了超出存档主要需求的功能。 <br /><br />在启用 Azure AD DS 安全审核事件之前，请先[创建 Azure 存储帐户](../storage/common/storage-account-create.md)。|
|Azure 事件中心| 当您的主要需求是与其他软件（如数据分析软件或安全信息&事件管理 （SIEM） 软件共享安全审核事件时，应使用此目标。<br /><br />在启用 Azure AD DS 安全审核事件之前，[请使用 Azure 门户创建事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure 日志分析工作区| 当您的主要需要是直接分析和查看 Azure 门户的安全审核时，应使用此目标。<br /><br />在启用 Azure AD DS 安全审核事件之前，[请在 Azure 门户中创建日志分析工作区。](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>使用 Azure 门户启用安全审核事件

要使用 Azure 门户启用 Azure AD DS 安全审核事件，请使用以下步骤完成。

> [!IMPORTANT]
> Azure AD DS 安全审核不具有追溯性。 无法从过去检索或重播事件。 Azure AD DS 只能发送启用安全审核后发生的事件。

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 在 Azure 门户的顶部，搜索并选择**Azure AD 域服务**。 选择托管域，如*aaddscontoso.com*。
1. 在 Azure AD DS 窗口中，选择左侧的**诊断设置**。
1. 默认情况下不配置任何诊断。 要开始，请选择 **"添加诊断设置**"。

    ![为 Azure AD 域服务添加诊断设置](./media/security-audit-events/add-diagnostic-settings.png)

1. 输入诊断配置的名称，如*adds 审核*。

    选中所需的安全审核目标复选框。 您可以从 Azure 存储帐户、Azure 事件中心或日志分析工作区中进行选择。 这些目标资源必须已存在于 Azure 订阅中。 无法在此向导中创建目标资源。

    ![启用所需的审核事件的目标和类型以捕获](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure 存储**
        * 选择 **"存档到存储帐户**"，然后选择"**配置**"。
        * 选择要用于存档安全审核事件的**订阅**和**存储帐户**。
        * 准备就绪后，选择 **"确定**"。
    * **Azure 事件中心**
        * 选择 **"流到事件中心**"，然后选择 **"配置**"。
        * 选择**订阅**和**事件中心命名空间**。 如果需要，还可以选择**事件中心名称**，然后选择**事件中心策略名称**。
        * 准备就绪后，选择 **"确定**"。
    * **Azure 日志分析工作区**
        * 选择 **"发送到日志分析**"，然后选择要用于存储安全审核事件的**订阅**和**日志分析工作区**。

1. 选择要为特定目标资源包括的日志类别。 如果将审核事件发送到 Azure 存储帐户，还可以配置定义保留数据的天数的保留策略。 默认设置*0*保留所有数据，并且不会在一段时间后旋转事件。

    您可以在单个配置中为每个目标资源选择不同的日志类别。 例如，此功能允许您选择要为日志分析保留的日志类别以及要存档的日志类别。

1. 完成后，选择 **"保存"** 以提交更改。 目标资源在保存配置后不久开始接收 Azure AD DS 安全审核事件。

## <a name="enable-security-audit-events-using-azure-powershell"></a>使用 Azure PowerShell 启用安全审核事件

要使用 Azure PowerShell 启用 Azure AD DS 安全审核事件，请使用以下步骤完成。 如果需要，请先[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> Azure AD DS 安全审核不具有追溯性。 无法从过去检索或重播事件。 Azure AD DS 只能发送启用安全审核后发生的事件。

1. 使用[连接-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 对 Azure 订阅进行身份验证。 出现提示后，输入您的帐户凭据。

    ```azurepowershell
    Connect-AzAccount
    ```

1. 为安全审核事件创建目标资源。

    * **Azure 存储** - [使用 Azure PowerShell 创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure 事件中心** - [使用 Azure PowerShell 创建事件中心](../event-hubs/event-hubs-quickstart-powershell.md)。 您可能还需要使用[New-AzEventHub 授权规则](/powershell/module/az.eventhub/new-azeventhubauthorizationrule)cmdlet 来创建授权规则，将 Azure AD DS 权限授予事件中心*命名空间*。 授权规则必须包括 **"管理**"、**侦听**和**发送**权限。

        > [!IMPORTANT]
        > 请确保在事件中心命名空间而不是事件中心本身上设置授权规则。

    * **Azure 日志分析工作区** - [使用 Azure PowerShell 创建日志分析工作区](../azure-monitor/learn/quick-create-workspace-posh.md)。

1. 使用[获取-AzResource](/powershell/module/Az.Resources/Get-AzResource) cmdlet 获取 Azure AD DS 托管域的资源 ID。 创建名为$aadds的变量 *。要*保存该值的资源 Id：

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. 使用["设置-Az诊断设置](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting)"cmdlet 配置 Azure 诊断设置，以便使用 Azure AD 域服务安全审核事件的目标资源。 在以下示例中，变量 *$aadds。资源 Id*从上一步使用。

    * **Azure 存储**- 将*存储帐户 Id*替换为存储帐户名称：

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure 事件中心**- 将*事件 HubName*替换为事件中心的名称，并将*事件HubRuleId*替换为授权规则 ID：

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure 日志分析工作区**- 将*工作区 Id*替换为日志分析工作区的 ID：

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>使用 Azure 监视器查询和查看安全审核事件

通过日志分析工作区，您可以使用 Azure 监视器和 Kusto 查询语言查看和分析安全审核事件。 此查询语言专为只读使用而设计，具有易于阅读的语法的电源分析功能。 有关开始使用 Kusto 查询语言的详细信息，请参阅以下文章：

* [Azure Monitor 文档](https://docs.microsoft.com/azure/azure-monitor/)
* [Azure Monitor 中的 Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)
* [Azure Monitor 中的日志查询入门](../azure-monitor/log-query/get-started-queries.md)
* [创建和共享 Log Analytics 数据的仪表板](../azure-monitor/learn/tutorial-logs-dashboards.md)

以下示例查询可用于开始分析 Azure AD DS 的安全审核事件。

### <a name="sample-query-1"></a>示例查询 1

查看过去七天的所有帐户锁定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>示例查询 2

查看 2020 年 2 月 3 日上午 9 点之间的所有帐户锁定事件 （*4740）。* 和 2020 年 2 月 10 日午夜，按日期和时间排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>示例查询 3

查看七天前（即日起）名为用户的帐户登录事件：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>示例查询 4

查看帐户登录事件七天前从现在开始，帐户命名用户试图使用错误密码登录 （*0xC0000006a*）：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>示例查询 5

查看帐户登录事件七天前从现在开始，帐户命名用户试图在帐户锁定时登录 （*0xC0000234*）：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>示例查询 6

查看七天前的帐户登录事件数，查看所有锁定用户发生的所有登录尝试：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>审核事件类别

Azure AD DS 安全审核与传统 AD DS 域控制器的传统审核一致。 在混合环境中，您可以重用现有的审核模式，以便在分析事件时使用相同的逻辑。 根据需要排除故障或分析的方案，需要针对不同的审核事件类别。

以下审核事件类别可用：

| 审核类别名称 | 说明 |
|:---|:---|
| 帐户登录|审核尝试对域控制器或本地安全帐户管理器 （SAM） 上的帐户数据进行身份验证。</p>登录和注销策略设置和事件跟踪访问特定计算机的尝试。 此类别中的设置和事件侧重于所使用的帐户数据库。 此类别包括以下子类别：<ul><li>[审核凭据验证](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[审核 Kerberos 身份验证服务](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[审核 Kerberos 服务票证操作](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[审核其他登录/注销事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 帐户管理|审核对用户和计算机帐户和组的更改。 此类别包括以下子类别：<ul><li>[审核应用程序组管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[审核计算机帐户管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[审核分发组管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[审核其他帐户管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[审核安全组管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[审核用户帐户管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 详细信息跟踪|审核该计算机上的单个应用程序和用户的活动，并了解计算机的使用方式。 此类别包括以下子类别：<ul><li>[审核 DPAPI 活动](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[审核 PNP 活动](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[审核进程创建](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[审核进程终止](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[审核 RPC 事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 目录服务访问|审核尝试访问和修改活动目录域服务 （AD DS） 中的对象。 这些审核事件仅记录在域控制器上。 此类别包括以下子类别：<ul><li>[审核详细的目录服务复制](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[审核目录服务访问](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[审核目录服务更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[审核目录服务复制](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 登录-注销|审核尝试以交互方式或通过网络登录到计算机。 这些事件可用于跟踪用户活动和识别对网络资源的潜在攻击。 此类别包括以下子类别：<ul><li>[审核帐户锁定](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[审核用户/设备声明](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[审核 IPsec 扩展模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[审核组成员身份](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[审核 IPsec 主模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[审核 IPsec 快速模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[审核注销](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[审核登录](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[审核网络策略服务器](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[审核其他登录/注销事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[审核特殊登录](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|对象访问| 审核尝试访问网络或计算机上的特定对象或对象类型。 此类别包括以下子类别：<ul><li>[审核生成的应用程序](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[审核证书服务](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[审核详细的文件共享](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[审核文件共享](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[审计文件系统](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[审核筛选平台连接](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[审核筛选平台数据包丢弃](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[审核句柄操作](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[审核内核对象](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[审核其他对象访问事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[审计登记处](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[审核可移动存储](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[审核 SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[审核中心访问策略暂存](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|策略更改|审核对本地系统或网络上的重要安全策略的更改。 策略通常由管理员建立，以帮助保护网络资源。 监视更改或尝试更改这些策略可能是网络安全管理的一个重要方面。 此类别包括以下子类别：<ul><li>[审核审核策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[审核身份验证策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[审核授权策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[审核筛选平台策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[审核 MPSSVC 规则级别策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[审核其他策略更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|权限使用| 审核对一个或多个系统使用某些权限。 此类别包括以下子类别：<ul><li>[审核非敏感权限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[审核敏感权限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[审核其他权限使用事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|系统| 审核对未包含在其他类别中且具有潜在安全隐患的计算机的系统级更改。 此类别包括以下子类别：<ul><li>[审核 IPsec 驱动程序](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[审核其他系统事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[审核安全状态更改](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[审核安全系统扩展](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[审核系统完整性](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>每个类别的事件 ID

 当特定操作触发可审核事件时，Azure AD DS 安全审核将记录以下事件 ID：

| 事件类别名称 | 事件 ID |
|:---|:---|
|帐户登录安全性|4767, 4774, 4775, 4776, 4777|
|帐户管理安全性|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|详细信息跟踪安全性|无|
|DS 访问安全性|5136, 5137, 5138, 5139, 5141|
|登录-注销安全性|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|对象访问安全性|无|
|策略更改安全性|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|特权 使用安全性|4985|
|系统安全|4612, 4621|

## <a name="next-steps"></a>后续步骤

有关 Kusto 的具体信息，请参阅以下文章：

* 库斯托查询语言的[概述](/azure/kusto/query/)。
* [Kusto 教程](/azure/kusto/query/tutorial)，让您熟悉查询基础知识。
* [帮助您](/azure/kusto/query/samples)学习查看数据的新方法的示例查询。
* Kusto[最佳实践](/azure/kusto/query/best-practices)，以优化您的查询以取得成功。

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
