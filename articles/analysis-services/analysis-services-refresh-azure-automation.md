---
title: 通过 Azure 自动化刷新 Azure Analysis Services 模型 |Microsoft Docs
description: 了解如何使用 Azure 自动化对模型进行代码刷新。
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: ed1634ef1009149dc2937174b20248eab9cd335f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294793"
---
# <a name="refresh-with-azure-automation"></a>使用 Azure 自动化进行刷新

通过使用 Azure 自动化和 PowerShell Runbook，你可以对 Azure 分析表格模型执行自动数据刷新操作。  

本文中的示例使用[PowerShell SqlServer 模块](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)。

本文后面提供了一个示例 PowerShell Runbook，演示如何刷新模型。  

## <a name="authentication"></a>身份验证

所有调用必须使用有效的 Azure Active Directory (OAuth 2) 令牌进行身份验证。  本文中的示例将使用服务主体（SPN）对 Azure Analysis Services 进行身份验证。

若要详细了解如何创建服务主体，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 下面的示例假定 Azure Analysis Services 防火墙已禁用。 如果启用了防火墙，则需要在防火墙中加入请求发起方的公共 IP 地址。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>从 PowerShell 库安装 SqlServer 模块。

1. 在 Azure 自动化帐户中，单击 "**模块**"，然后单击 "**浏览库**"。

2. 在搜索栏中，搜索**SqlServer**。

    ![搜索模块](./media/analysis-services-refresh-azure-automation/1.png)

3. 选择 "SqlServer"，然后单击 "**导入**"。
 
    ![导入模块](./media/analysis-services-refresh-azure-automation/2.png)

4. 单击 **“确定”** 。
 
### <a name="create-a-service-principal-spn"></a>创建服务主体 (SPN)

若要了解如何创建服务主体，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中配置权限
 
创建的服务主体必须对服务器拥有服务器管理员权限。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

## <a name="design-the-azure-automation-runbook"></a>设计 Azure 自动化 Runbook

1. 在自动化帐户中，创建将用于安全存储服务主体的**凭据**资源。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/6.png)

2. 输入凭据的详细信息。  对于 "**用户名**"，请输入**spn ClientId**，对于**密码**，请输入**spn 机密**。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/7.png)

3. 导入自动化 Runbook

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. 浏览**Refresh-Model**文件，提供**名称**和**说明**，然后单击 "**创建**"。

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. 创建 Runbook 后，它会自动进入编辑模式。  选择“发布”。

    ![发布 Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 使用**get-automationpscredential**命令，runbook 检索先前创建的凭据资源。  然后，将此命令传递给**ProcessASADatabase** PowerShell 命令，以执行 Azure Analysis Services 身份验证。

6. 单击 "**启动**" 测试 runbook。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. 填写**DATABASENAME**、 **ANALYSISSERVER**和**REFRESHTYPE**参数，然后单击 **"确定"** 。 手动运行 Runbook 时，不需要**WEBHOOKDATA**参数。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/12.png)

如果成功执行了 Runbook，则将收到如下所示的输出：

![成功运行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>使用独立的 Azure 自动化 Runbook

可以将 Runbook 配置为按计划触发 Azure Analysis Services 模型刷新。

可按如下所示进行配置：

1. 在自动化 Runbook 中，单击 "**计划**"，然后单击 "**添加计划**"。
 
    ![创建计划](./media/analysis-services-refresh-azure-automation/14.png)

2. 单击 "**计划** > **创建新计划**，然后填写详细信息。

    ![配置计划](./media/analysis-services-refresh-azure-automation/15.png)

3. 单击“创建”。

4. 填写计划的参数。 它们将在每次触发 Runbook 时使用。 通过计划运行时， **WEBHOOKDATA**参数应留空。

    ![配置参数](./media/analysis-services-refresh-azure-automation/16.png)

5. 单击 **“确定”** 。

## <a name="consume-with-data-factory"></a>使用数据工厂

若要使用 Azure 数据工厂来使用 runbook，请首先为 runbook 创建**Webhook** 。 **Webhook**将提供可通过 Azure 数据工厂 web 活动调用的 URL。

> [!IMPORTANT]
> 若要创建**Webhook**，必须**发布**Runbook 的状态。

1. 在自动化 Runbook 中，单击 " **webhook**"，并单击 "**添加 Webhook**"。

   ![添加 Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. 为 Webhook 指定名称和过期时间。  该名称仅在自动化 Runbook 内标识 Webhook，而不构成 URL 的一部分。

   >[!CAUTION]
   >请确保在关闭向导之前复制 URL，因为在关闭向导后无法再返回。
    
   ![配置 Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook 的参数可以保留为空。  配置 Azure 数据工厂 web 活动时，可以将参数传递到 web 调用的正文中。

3. 在数据工厂中，配置**web 活动**

### <a name="example"></a>示例

   ![示例 Web 活动](./media/analysis-services-refresh-azure-automation/19.png)

**Url**是从 Webhook 创建的 url。

**正文**是包含以下属性的 JSON 文档：


|属性  |ReplTest1  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 数据库的名称 <br/> 例如：AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services 服务器名称。 <br/> 示例： https： \//westus/servers/asazure/模型/AdventureWorks/         |
|**DatabaseRefreshType**     |要执行的刷新类型。 <br/> 例如：完整         |

示例 JSON 正文：

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

这些参数在 runbook PowerShell 脚本中定义。  当执行 web 活动时，传递的 JSON 有效负载为 WEBHOOKDATA。

这会被反序列化并存储为 PowerShell 参数，然后 ProcesASDatabase PowerShell 命令将使用该参数。

![反序列化 Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>将混合辅助角色用于 Azure Analysis Services

具有静态公共 IP 地址的 Azure 虚拟机可用作 Azure 自动化混合辅助角色。  然后，可以将此公共 IP 地址添加到 Azure Analysis Services 防火墙中。

> [!IMPORTANT]
> 确保将虚拟机公共 IP 地址配置为静态。
>
>若要详细了解如何配置 Azure 自动化混合辅助角色，请参阅[使用混合 Runbook 辅助角色自动执行数据中心或云中的资源](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)。

配置混合辅助角色后，请按照使用[数据工厂使用](#consume-with-data-factory)部分中所述创建一个 Webhook。  唯一的区别在于，在配置 Webhook 时，请选择 "在  > **混合辅助角色** **上运行**" 选项。

使用混合辅助角色的 webhook 示例：

![混合辅助角色 Webhook 示例](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell Runbook 示例

下面的代码片段举例说明如何使用 PowerShell Runbook 执行 Azure Analysis Services 模型刷新。

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
