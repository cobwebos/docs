---
title: 通过 Azure 自动化刷新 Azure Analysis Services 模型 | Microsoft Docs
description: 本文介绍了如何使用 Azure 自动化为 Azure Analysis Services 编写模型刷新代码。
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: chlound
ms.openlocfilehash: bbbc2863e06b4602a4175d46bbe21414041583ba
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926555"
---
# <a name="refresh-with-azure-automation"></a>使用 Azure 自动化进行刷新

通过使用 Azure 自动化和 PowerShell Runbook，你可以对 Azure Analysis 表格模型执行自动化数据刷新操作。  

本文中的示例使用了 [PowerShell SqlServer 模块](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)。

本文后面提供了一个示例 PowerShell Runbook，它演示了如何刷新模型。  

## <a name="authentication"></a>身份验证

所有调用都必须使用有效的 Azure Active Directory (OAuth 2) 令牌进行身份验证。  本文中的示例将使用服务主体 (SPN) 通过 Azure Analysis Services 进行身份验证。

若要详细了解如何创建服务主体，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 下面的示例假定 Azure Analysis Services 防火墙被禁用。 如果防火墙已启用，则需要将请求发起程序的公共 IP 地址添加到防火墙的白名单中。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>从 PowerShell 库中安装 SqlServer 模块。

1. 在你的 Azure 自动化帐户中，单击“模块”，然后单击“浏览库”。 

2. 在搜索栏中搜索“SqlServer”。

    ![搜索模块](./media/analysis-services-refresh-azure-automation/1.png)

3. 选择 SqlServer，然后单击“导入”。
 
    ![导入模块](./media/analysis-services-refresh-azure-automation/2.png)

4. 单击“确定”。
 
### <a name="create-a-service-principal-spn"></a>创建服务主体 (SPN)

若要了解如何创建服务主体，请参阅[使用 Azure 门户创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中配置权限
 
你创建的服务主体必须对服务器拥有服务器管理员权限。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

## <a name="design-the-azure-automation-runbook"></a>设计 Azure 自动化 Runbook

1. 在自动化帐户中，创建一个**凭据**资源，该资源将用来安全地存储服务主体。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/6.png)

2. 输入凭据的详细信息。 在“用户名”中，输入服务主体应用程序 Id (appid)，然后在“密码”中输入服务主体机密。

    ![创建凭据](./media/analysis-services-refresh-azure-automation/7.png)

3. 导入自动化 Runbook

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. 以浏览方式找到“Refresh-Model.ps1”文件，提供“名称”和“说明”，然后单击“创建”。

    ![导入 Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. 创建 Runbook 后，它将自动进入编辑模式。  选择“发布”。

    ![发布 Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Runbook 使用 **Get-AutomationPSCredential** 命令检索先前创建的凭据资源。  然后，此命令将传递给 **Invoke-ProcessASADatabase** PowerShell 命令，以便通过 Azure Analysis Services 执行身份验证。

6. 通过单击“启动”来测试 Runbook。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. 填写“DATABASENAME”、“ANALYSISSERVER”和“REFRESHTYPE”参数，然后单击“确定”。 手动运行 Runbook 时，不需要“WEBHOOKDATA”参数。

    ![启动 Runbook](./media/analysis-services-refresh-azure-automation/12.png)

如果成功执行了 Runbook，则会收到如下所示的输出：

![成功的运行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>使用自包含 Azure 自动化 Runbook

可以将 Runbook 配置为按日程安排触发 Azure Analysis Services 模型刷新。

可以将其配置如下：

1. 在自动化 Runbook 中，单击“日程安排”，然后单击“添加日程安排”。
 
    ![创建日程安排](./media/analysis-services-refresh-azure-automation/14.png)

2. 单击“日程安排” > “新建日程安排”，然后填写详细信息。

    ![配置日程安排](./media/analysis-services-refresh-azure-automation/15.png)

3. 单击“创建”。

4. 为日程安排填写参数。 它们将在 Runbook 每次触发时使用。 通过日程安排运行时，“WEBHOOKDATA”参数应保留为空。

    ![配置参数](./media/analysis-services-refresh-azure-automation/16.png)

5. 单击“确定”。

## <a name="consume-with-data-factory"></a>通过数据工厂使用

若要通过 Azure 数据工厂来使用 runbook，请首先为 runbook 创建一个 **Webhook**。 **Webhook** 将提供可通过 Azure 数据工厂 Web 活动调用的 URL。

> [!IMPORTANT]
> 若要创建 **Webhook**，Runbook 的状态必须为“已发布”。

1. 在自动化 Runbook 中，单击“Webhook”，然后单击“添加 Webhook”。

   ![添加 Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. 为 Webhook 指定名称和有效期。  该名称仅在自动化 Runbook 内标识 Webhook，而不构成 URL 的一部分。

   >[!CAUTION]
   >请确保在关闭向导之前复制 URL，因为在关闭向导后就再也无法获得它了。
    
   ![配置 Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook 的参数可以保留为空。  配置 Azure 数据工厂 Web 活动时，可以将参数传递到 Web 调用的正文中。

3. 在数据工厂中，配置一个 **Web 活动**

### <a name="example"></a>示例

   ![Web 活动示例](./media/analysis-services-refresh-azure-automation/19.png)

**URL** 是基于 Webhook 创建的 URL。

**正文**是一个应当包含以下属性的 JSON 文档：


|properties  |值  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 数据库的名称 <br/> 示例：AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services 服务器名称。 <br/> 示例：https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |要执行的刷新的类型。 <br/> 示例：完全         |

JSON 正文示例：

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

这些参数是在 runbook PowerShell 脚本中定义的。  当执行 Web 活动时，传递的 JSON 有效负载为 WEBHOOKDATA。

这会被反序列化并存储为 PowerShell 参数，然后 PowerShell 命令 Invoke-ProcesASDatabase 将使用这些参数。

![反序列化的 Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>将混合辅助角色用于 Azure Analysis Services

具有静态公共 IP 地址的 Azure 虚拟机可以用作 Azure 自动化混合辅助角色。  然后，可以将此公共 IP 地址添加到 Azure Analysis Services 防火墙中。

> [!IMPORTANT]
> 请确保将虚拟机公共 IP 地址配置为静态的。
>
>若要详细了解如何配置 Azure 自动化混合辅助角色，请参阅[混合 Runbook 辅助角色安装](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation)。

配置混合辅助角色后，请按照[通过数据工厂使用](#consume-with-data-factory)部分中所述创建一个 Webhook。  此处的唯一区别在于，在配置 Webhook 时，请选择“运行于” > “混合辅助角色”选项。

使用了混合辅助角色的 Webhook 示例：

![混合辅助角色 Webhook 示例](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell Runbook 示例

下面的代码片段举例说明了如何使用 PowerShell Runbook 执行 Azure Analysis Services 模型刷新。

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
