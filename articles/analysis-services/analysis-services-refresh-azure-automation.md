---
title: 刷新 Azure Analysis Services 模型与 Azure 自动化 |Microsoft Docs
description: 了解如何通过使用 Azure 自动化的代码模型刷新。
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 4cae93cff594ad561973f8029ea7335dc4c60263
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357003"
---
# <a name="refresh-with-azure-automation"></a>使用 Azure 自动化进行刷新

通过使用 Azure 自动化和 PowerShell Runbook，可以执行 Azure 分析表格模型上的自动的数据刷新操作。  

这篇文章中的示例使用[PowerShell SqlServer 模块](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)。

在本文后面提供示例 PowerShell Runbook，该示例演示了刷新模型。  

## <a name="authentication"></a>Authentication

所有调用必须使用有效的 Azure Active Directory (OAuth 2) 令牌进行身份都验证。  此文章中的示例将使用服务主体 (SPN) 对 Azure Analysis Services 进行身份验证。

若要了解有关创建服务主体的详细信息，请参阅[通过使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> 下面的示例假定 Azure Analysis Services 防火墙处于禁用状态。 如果启用了防火墙，然后需要在防火墙中列入允许列表的请求发起程序的公共 IP 地址。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>从 PowerShell 库安装 SqlServer 模块。

1. 在 Azure 自动化帐户中，单击**模块**，然后**浏览库**。

2. 在搜索栏中，搜索**SqlServer**。

    ![搜索模块](./media/analysis-services-refresh-azure-automation/1.png)

3. 选择 SqlServer，然后单击**导入**。
 
    ![导入模块](./media/analysis-services-refresh-azure-automation/2.png)

4. 单击“确定”。 
 
### <a name="create-a-service-principal-spn"></a>创建服务主体 (SPN)

若要了解如何创建服务主体，请参阅[通过使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中配置权限
 
您创建的服务主体必须具有服务器管理员权限在服务器上。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

## <a name="design-the-azure-automation-runbook"></a>设计 Azure 自动化 Runbook

1. 在自动化帐户中，创建**凭据**资源，用于安全地存储服务主体。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/6.png)

2. 输入凭据的详细信息。  有关**用户名**，输入**SPN ClientId**，为**密码**，输入**SPN 机密**。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/7.png)

3. 导入自动化 Runbook

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. 浏览**刷新 Model.ps1**文件中，提供**名称**并**说明**，然后单击**创建**。

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. 创建 Runbook 后，它将自动进入编辑模式。  选择“发布”  。

    ![发布 Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 已创建的凭据资源之前检索 runbook 使用**Get-automationpscredential**命令。  然后将此命令传递给**Invoke ProcessASADatabase** PowerShell 命令来执行到 Azure Analysis Services 身份验证。

6. 通过单击测试 runbook**启动**。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. 填写**DATABASENAME**， **ANALYSISSERVER**，并**REFRESHTYPE**参数，并单击**确定**。 **WEBHOOKDATA**手动运行 Runbook 时不需要参数。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/12.png)

如果 Runbook 已成功执行，则会收到如下所示的输出：

![成功运行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>使用独立的 Azure 自动化 Runbook

Runbook 可以配置为按计划定期刷新 Azure Analysis Services 模型的触发器。

这可以按如下所示进行配置：

1. 在自动化 Runbook 中，单击**计划**，然后**添加计划**。
 
    ![创建计划](./media/analysis-services-refresh-azure-automation/14.png)

2. 单击**计划** > **创建新计划**，然后再填写详细信息。

    ![配置计划](./media/analysis-services-refresh-azure-automation/15.png)

3. 单击**创建**。

4. 填写计划参数。 这将用每次触发 Runbook。 **WEBHOOKDATA**参数应留空时通过计划运行。

    ![配置参数](./media/analysis-services-refresh-azure-automation/16.png)

5. 单击“确定”。 

## <a name="consume-with-data-factory"></a>使用数据工厂

若要通过使用 Azure 数据工厂中使用 runbook，首先创建**Webhook** runbook。 **Webhook**将提供可以通过 Azure 数据工厂 web 活动调用的 URL。

> [!IMPORTANT]
> 若要创建**Webhook**，Runbook 的状态必须是**已发布**。

1. 在自动化 Runbook 中，单击**Webhook**，然后单击**添加 Webhook**。

   ![添加 Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. 为 Webhook 提供一个名称和到期时间。  名称只能识别在自动化 Runbook Webhook，它不会形成 URL 的一部分。

   >[!CAUTION]
   >请确保在关闭向导，如无法取回它一次关闭之前复制的 URL。
    
   ![配置 Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook 的参数可以保留为空。  在配置的 Azure 数据工厂 web 活动时，可以将参数传入 web 调用的正文。

3. 在数据工厂中，配置**web 活动**

### <a name="example"></a>示例

   ![示例 Web 活动](./media/analysis-services-refresh-azure-automation/19.png)

**URL**是创建的 Webhook 的 URL。

**正文**是一个 JSON 文档，其中应包含以下属性：


|属性  |值  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 数据库的名称 <br/> 示例：AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services 服务器名称。 <br/> 示例： https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |若要执行的刷新的类型。 <br/> 示例：完整         |

JSON 正文示例：

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Runbook PowerShell 脚本中定义了这些参数。  Web 活动执行时，传递 JSON 有效负载是 WEBHOOKDATA。

这是反序列化，存储作为 PowerShell 参数，然后使用 Invoke ProcesASDatabase PowerShell 命令。

![反序列化的 Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>使用 Azure Analysis Services 使用混合辅助角色

Azure 虚拟机具有静态公共 IP 地址可以用作 Azure 自动化混合辅助角色。  然后可以将此公共 IP 地址添加到 Azure Analysis Services 防火墙。

> [!IMPORTANT]
> 确保虚拟机的公共 IP 地址配置为静态。
>
>若要了解有关配置 Azure 自动化混合辅助角色的详细信息，请参阅[使用混合 Runbook 辅助角色自动在数据中心或云资源](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)。

混合辅助角色配置后，创建 Webhook 部分中所述[使用数据工厂使用](#consume-with-data-factory)。  唯一的区别是选择**上运行** > **混合辅助角色**选项配置 Webhook 时。

使用混合辅助角色的示例 webhook:

![混合辅助角色的示例 Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>示例 PowerShell Runbook

以下代码片段示范了如何执行 Azure Analysis Services 模型刷新使用 PowerShell Runbook。

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
