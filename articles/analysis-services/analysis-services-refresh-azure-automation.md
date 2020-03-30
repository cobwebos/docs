---
title: 使用 Azure 自动化刷新 Azure 分析服务模型 |微软文档
description: 本文介绍如何使用 Azure 自动化为 Azure 分析服务编写模型刷新代码。
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572356"
---
# <a name="refresh-with-azure-automation"></a>使用 Azure 自动化进行刷新

通过使用 Azure 自动化和 PowerShell Runbook，可以在 Azure 分析表格模型上执行自动数据刷新操作。  

本文中的示例使用[PowerShell SqlServer 模块](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)。

本文稍后将提供一个示例 PowerShell Runbook，该功能演示了刷新模型。  

## <a name="authentication"></a>身份验证

所有调用必须使用有效的 Azure Active Directory (OAuth 2) 令牌进行身份验证。  本文中的示例将使用服务主体 （SPN） 对 Azure 分析服务进行身份验证。

要了解有关创建服务主体的更多内容，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 以下示例假定 Azure 分析服务防火墙已禁用。 如果启用了防火墙，则请求开始器的公共 IP 地址需要在防火墙中白名单。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>从 PowerShell 库中安装 SqlServer 模块。

1. 在 Azure 自动化帐户中，单击 **"模块**"，然后单击 **"浏览库**"。

2. 在搜索栏中，搜索**SqlServer**。

    ![搜索模块](./media/analysis-services-refresh-azure-automation/1.png)

3. 选择 SqlServer，然后单击 **"导入**"。
 
    ![导入模块](./media/analysis-services-refresh-azure-automation/2.png)

4. 单击“确定”。
 
### <a name="create-a-service-principal-spn"></a>创建服务主体 (SPN)

若要了解如何创建服务主体，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中配置权限
 
创建的服务主体必须对服务器拥有服务器管理员权限。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

## <a name="design-the-azure-automation-runbook"></a>设计 Azure 自动化运行簿

1. 在自动化帐户中，创建一个**凭据**资源，用于安全地存储服务主体。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/6.png)

2. 输入凭据的详细信息。  对于**用户名**，输入**SPN 客户端Id**，对于**密码**，输入**SPN 密钥**。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/7.png)

3. 导入自动化运行簿

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. 浏览**刷新模型.ps1**文件，提供**名称**和**说明**，然后单击"**创建**"。

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. 创建 Runbook 后，它将自动进入编辑模式。  选择“发布”。

    ![发布 Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 以前创建的凭据资源由 Runbook 使用**获取自动化PS凭据**命令检索。  然后，此命令将传递给**调用进程数据库**PowerShell 命令，以执行对 Azure 分析服务的身份验证。

6. 单击"**开始"** 测试 Runbook。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. 填写**数据库名称**、**分析服务器**和**REFRESHTYPE**参数，然后单击"**确定**"。 手动运行 Runbook 时，不需要**WEBHOOKDATA**参数。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/12.png)

如果 Runbook 成功执行，您将收到如下所示的输出：

![成功运行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>使用自包含的 Azure 自动化 Runbook

Runbook 可以配置为按计划触发 Azure 分析服务模型刷新。

这可以配置如下：

1. 在"自动化 Runbook"中，单击 **"计划**"，然后**添加计划**。
 
    ![创建计划](./media/analysis-services-refresh-azure-automation/14.png)

2. 单击 **"计划** > **创建新计划**"，然后填写详细信息。

    ![配置计划](./media/analysis-services-refresh-azure-automation/15.png)

3. 单击 **“创建”**。

4. 填写计划的参数。 每次 Runbook 触发时都将使用这些功能。 通过计划运行时 **，WEBHOOKDATA**参数应留空。

    ![配置参数](./media/analysis-services-refresh-azure-automation/16.png)

5. 单击“确定”。

## <a name="consume-with-data-factory"></a>使用数据工厂

要使用 Azure 数据工厂使用 Runbook，请先为 Runbook 创建**Webhook。** **Webhook**将提供一个 URL，可通过 Azure 数据工厂 Web 活动调用该 URL。

> [!IMPORTANT]
> 要创建**Webhook，** 必须**发布**Runbook 的状态。

1. 在自动化 Runbook 中，单击 **"网页挂钩**"，然后单击"**添加网页钩**"。

   ![添加网钩](./media/analysis-services-refresh-azure-automation/17.png)

2. 为 Webhook 指定名称和过期。  名称仅标识自动化 Runbook 中的 Webhook，它不构成 URL 的一部分。

   >[!CAUTION]
   >请确保在关闭向导之前复制 URL，因为关闭后无法将其找回。
    
   ![配置 Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    网钩的参数可以保持空。  配置 Azure 数据工厂 Web 活动时，参数可以传递到 Web 调用的主体中。

3. 在数据工厂中，配置**Web 活动**

### <a name="example"></a>示例

   ![示例 Web 活动](./media/analysis-services-refresh-azure-automation/19.png)

**URL**是从 Webhook 创建的 URL。

**正文**是 JSON 文档，应包含以下属性：


|properties  |“值”  |
|---------|---------|
|**分析服务数据库**     |Azure 分析服务数据库的名称 <br/> 示例：冒险工厂DB         |
|**分析服务服务器**     |Azure 分析服务服务器名称。 <br/> 示例： https：\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**数据库刷新类型**     |要执行的刷新类型。 <br/> 示例： 已满         |

JSON 正文示例：

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

这些参数在 runbook PowerShell 脚本中定义。  执行 Web 活动时，传递的 JSON 有效负载为 WEBHOOKDATA。

这将反序列化并存储为 PowerShell 参数，然后由 Invoke-ProcesAS 数据库 PowerShell 命令使用。

![反序列化网络钩子](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>将混合辅助角色与 Azure 分析服务一起使用

具有静态公共 IP 地址的 Azure 虚拟机可用作 Azure 自动化混合工作线程。  然后，可以将此公共 IP 地址添加到 Azure 分析服务防火墙。

> [!IMPORTANT]
> 确保虚拟机公共 IP 地址配置为静态。
>
>要了解有关配置 Azure 自动化混合工作线程的更多信息，请参阅[使用混合 Runbook 辅助角色在数据中心或云中自动执行资源](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)。

配置混合辅助角色后，按照"[使用数据工厂"](#consume-with-data-factory)一节中所述创建 Webhook。  此处的唯一区别是在配置 Webhook 时选择"**在混合辅助角色****上运行** > "选项。

使用混合辅助角色的示例 Webhook：

![混合工作线程 Webhook 示例](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>示例电源壳运行簿

以下代码段是如何使用 PowerShell Runbook 执行 Azure 分析服务模型刷新的示例。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>后续步骤

[示例](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
