---
title: Azure 中的 Office 365 管理解决方案 | Microsoft Docs
description: 本文详细介绍如何配置和使用 Azure 中的 Office 365 解决方案。  包括详细阐释 Log Analytics 中创建的 Office 365 记录。
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: e93860328ed6a31b7a06cc3420fb50ab4af9a00c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236097"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure 中的 Office 365 管理解决方案（预览）

![Office 365 徽标](media/oms-solution-office-365/icon.png)

通过 Office 365 管理解决方案，可在 Log Analytics 中监视 Office 365 环境。

- 监视 Office 365 帐户的用户活动，以分析使用模式和确定行为趋势。 例如，可提取特定使用方案，例如组织外共享的文件或最常用的 SharePoint 网站。
- 监视管理员活动，以跟踪配置更改或高特权操作。
- 检测并调查多余的用户行为，此操作可根据组织需求进行自定义。
- 演示审核和符合性。 例如，可监视对机密文件的文件访问操作，这对审核和符合性进程有所帮助。
- 通过对组织的 Office 365 活动数据使用[日志搜索](../log-analytics/log-analytics-log-search.md)，执行操作故障排除。

## <a name="prerequisites"></a>先决条件
需要以下各项才能安装和配置此解决方案。

- 组织的 Office 365 订阅。
- 充当全局管理员的用户帐户的凭据。
- 若要接收审核数据，必须在 Office 365 订阅中[配置审核](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)。  请注意，[邮箱审核](https://technet.microsoft.com/library/dn879651.aspx)单独配置。  若未配置审核，仍可安装解决方案和收集其他数据。
 

## <a name="management-packs"></a>管理包
此解决方案不会在[连接的管理组](../log-analytics/log-analytics-om-agents.md)中安装任何管理包。
  
## <a name="install-and-configure"></a>安装和配置
首先，[将 Office 365 解决方案添加到你的订阅](/monitoring/monitoring-solutions.md#install-a-management-solution)。 在添加后，必须执行本部分中的配置步骤来向其授予对你的 Office 365 订阅的访问权限。

### <a name="required-information"></a>必需的信息
在开始此过程之前，收集以下信息。

从 Log Analytics 工作区中：

- 工作区名称：将在其中收集 Office 365 数据的工作区。
- 资源组名称：包含工作区的资源组。
- Azure 订阅 ID：包含工作区的订阅。

从 Office 365 订阅中：

- 用户名：管理帐户的电子邮件地址。
- 租户 ID：Office 365 订阅的唯一 ID。
- 客户端 ID：一个 16 字符的字符串，表示 Office 365 客户端。
- 客户端机密：进行身份验证所需的已加密字符串。

### <a name="create-an-office-365-application-in-azure-active-directory"></a>在 Azure Active Directory 中创建一个 Office 365 应用程序
第一步是在 Azure Active Directory 中创建管理解决方案将用来访问 Office 365 解决方案的应用程序。

1. 通过 [https://portal.azure.com](https://portal.azure.com/) 登录到 Azure 门户。
1. 依次选择“Azure Active Directory”和“应用注册”。
1. 单击“新建应用程序注册”。

    ![添加应用注册](media/oms-solution-office-365/add-app-registration.png)
1. 输入应用程序**名称**和**登录 URL**。  名称应该是描述性的。  使用 _http://localhost_ 作为 URL，将“应用程序类型”保留为“Web 应用 / API”。
    
    ![创建应用程序](media/oms-solution-office-365/create-application.png)
1. 单击“创建”并验证应用程序信息。

    ![已注册的应用](media/oms-solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>为 Office 365 配置应用程序

1. 单击“设置”以打开“设置”菜单。
1. 选择“属性”。 将“多租户”更改为“是”。

    ![设置多租户](media/oms-solution-office-365/settings-multitenant.png)

1. 在“设置”菜单中选择“所需权限”，然后单击“添加”。
1. 单击“选择 API”，然后单击“Office 365 管理 API”。 单击“Office 365 管理 API”。 单击“选择”。

    ![选择 API](media/oms-solution-office-365/select-api.png)

1. 在“选择权限”下，为“应用程序权限”和“委派的权限”选择以下选项：
    - 读取组织的服务运行状况信息
    - 读取组织的活动数据
    - 读取组织的活动报表

    ![选择 API](media/oms-solution-office-365/select-permissions.png)

1. 依次单击“选择”、“完成”。
1. 单击“授予权限”，然后在要求确认时单击“是”。

    ![授予权限](media/oms-solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>为应用程序添加密钥

1. 在“设置”窗口中选择“密钥”。
1. 键入新密钥的**说明**和**持续时间**。
1. 单击“保存”，然后复制生成的**值**。

    ![密钥](media/oms-solution-office-365/keys.png)

### <a name="add-admin-consent"></a>添加管理员同意
若要首次启用管理帐户，必须为应用程序提供管理同意。 可以使用 PowerShell 脚本执行此操作。 

1. 将以下脚本保存为 *office365_consent.ps1*。

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
        
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    $mmsDomain = 'login.mms.microsoft.com'
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocationShort
    $WorkspaceLocation
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us';
                             $mmsDomain = 'usbn1.login.oms.microsoft.us'; break} # US Gov Virginia
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $OfficeAppRedirectUrl="https://$($WorkspaceLocationShort).$($mmsDomain)/Office365/Authorize"
        $OfficeAppRedirectUrl
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345&redirect_uri=$($OfficeAppRedirectUrl)"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. 使用以下命令运行该脚本。
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    示例：

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. 将会显示类似于以下窗口的窗口。 单击“接受”。
    
    ![管理员同意](media/oms-solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>订阅 Log Analytics 工作区
最后一步是让应用程序订阅 Log Analytics 工作区。 也是使用 PowerShell 脚本执行此操作。

1. 将以下脚本保存为 *office365_subscription.ps1*。

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. 使用以下命令运行该脚本：
    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```
    示例：

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troublshooting"></a>故障排除

在订阅已存在后，如果尝试创建订阅，则会出现以下错误。

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

如果提供了无效的参数值，则会出现以下错误。

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>卸载
可以使用[删除管理解决方案](../monitoring/monitoring-solutions.md#remove-a-management-solution)中的过程删除 Office 365 管理解决方案。 但是，这不会停止将数据从 Office 365 收集到 Log Analytics 中。 请按照下面的过程来取消订阅 Office 365 并停止收集数据。

1. 将以下脚本保存为 *office365_unsubscribe.ps1*。

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. 使用以下命令运行该脚本：

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    示例：

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>数据收集
### <a name="supported-agents"></a>支持的代理
Office 365 解决方案不会从任何 [OMS 代理](../log-analytics/log-analytics-data-sources.md)中检索数据。  而直接从 Office 365 检索数据。

### <a name="collection-frequency"></a>收集频率
初次收集数据可能需要几个小时。 在开始收集后，每次创建记录时，Office 365 都会向 Log Analytics 发送带详细数据的 [webhook 通知](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications)。 在收到此记录几分钟后，此记录将出现在 Log Analytics 中。

## <a name="using-the-solution"></a>使用解决方案
向 Log Analytics 工作区添加 Office 365 解决方案时，“Office 365”磁贴将添加到你的仪表板。 此磁贴显示环境中计算机数量及其更新符合性的计数和图形表示形式。<br><br>
![Office 365 摘要磁贴](media/oms-solution-office-365/tile.png)  

单击“Office 365”磁贴，打开“Office 365”仪表板。

![Office 365 仪表板](media/oms-solution-office-365/dashboard.png)  

仪表板包含下表中的列。 每个列按照指定范围和时间范围内符合该列条件的计数列出了前十个警报。 可通过以下方式运行提供整个列表的日志搜索：单击该列底部的“全部查看”或单击列标题。

| 列 | 说明 |
|:--|:--|
| 操作 | 提供所有监视的 Office 365 订阅中的活动用户相关信息。 还能够看到随着时间的推移发生的活动数。
| Exchange | 显示 Exchange Server 活动的明细，例如 Add-Mailbox 权限或 Set-Mailbox。 |
| SharePoint | 显示用户在 SharePoint 文档上执行次数最多的一些活动。 从此磁贴向下钻取时，搜索页会显示这些活动的详细信息，例如目标文档和此活动的位置。 比如，对于文件访问事件，将能够看到正在访问的文档、其关联的帐户名以及 IP 地址。 |
| Azure Active Directory | 包含一些最活跃的用户活动，例如重置用户密码和登录尝试。 向下钻取时，将能够看到这些活动的详细信息（例如结果状态）。 如果想要监视 Azure Active Directory 上的可疑活动，这通常很有帮助。 |




## <a name="log-analytics-records"></a>Log Analytics 记录

Office 365 解决方案在 Log Analytics 工作区中创建的所有记录都具有 OfficeActivity 类型。  OfficeWorkload 属性确定记录所指的 Office 365 服务 - Exchange、AzureActiveDirectory、SharePoint 或 OneDrive。  RecordType 属性指定操作的类型。  每种操作类型的属性都不同，详情请见下表。

### <a name="common-properties"></a>通用属性
以下属性对于所有 Office 365 记录通用。

| 属性 | 说明 |
|:--- |:--- |
| Type | OfficeActivity |
| ClientIP | 记录活动时使用的设备的 IP 地址。 IP 地址以 IPv4 或 IPv6 地址格式显示。 |
| OfficeWorkload | 记录所指的 Office 365 服务。<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operation | 用户或管理员活动的名称。  |
| OrganizationId | 组织的 Office 365 租户的 GUID。 无论发生在哪种 Office 365 服务中，组织中的此值均保持不变。 |
| RecordType | 所执行操作的类型。 |
| ResultStatus | 指示操作（在 Operation 属性中指定）是成功还是失败。 可能的值有 Succeeded、PartiallySucceded 或 Failed。 对于 Exchange 管理员活动，值为 True 或 False。 |
| UserId | 执行使系统记下记录的操作的用户的 UPN（用户主体名称），例如 my_name@my_domain_name。 请注意，还包括系统帐户（例如 SHAREPOINT\system 或 NTAUTHORITY\SYSTEM）执行的活动的记录。 | 
| UserKey | UserId 属性中标识的用户的备用 ID。  例如，此属性由 SharePoint、OneDrive for Business 和 Exchange 中用户执行的事件的 Passport 唯一 ID (PUID) 进行填充。 此属性还可为其他服务中发生的事件以及系统帐户执行的事件指定与 UserID 属性相同的值|
| UserType | 执行操作的用户的类型。<br><br>管理员<br>Application<br>DcAdmin<br>常规<br>保留<br>服务主体<br>系统 |


### <a name="azure-active-directory-base"></a>Azure Active Directory Base
以下属性对于所有 Azure Active Directory 记录通用。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD 事件的类型。 |
| ExtendedProperties | Azure AD 事件的扩展属性。 |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory 帐户登录
Active Directory 用户尝试登录时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Application | 触发帐户登录事件的应用程序，如 Office 15。 |
| Client | 有关客户端设备、设备操作系统和用于帐户登录事件的设备浏览器的详细信息。 |
| LoginStatus | 此属性直接从 OrgIdLogon.LoginStatus 获取。 可通过警报算法完成各种关注的登录失败的映射。 |
| UserDomain | 租户标识信息 (TII)。 | 


### <a name="azure-active-directory"></a>Azure Active Directory
更改 Azure Active Directory 对象或向其添加内容时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | 所执行操作（由 Operation 属性标识）针对的用户。 |
| Actor | 执行操作的用户或服务主体。 |
| ActorContextId | 参与者所属的组织的 GUID。 |
| ActorIpAddress | 采用 IPV4 或 IPV6 地址格式的参与者 IP 地址。 |
| InterSystemsId | 跨 Office 365 服务内的组件跟踪操作的 GUID。 |
| IntraSystemId |   由 Azure Active Directory 生成用于跟踪操作的 GUID。 |
| SupportTicketId | “代表执行”情况下的操作的客户支持票证 ID。 |
| TargetContextId | 目标用户所属的组织的 GUID。 |


### <a name="data-center-security"></a>数据中心安全
基于数据中心安全审核数据创建这些记录。  

| 属性 | 说明 |
|:--- |:--- |
| EffectiveOrganization | 提升/cmdlet 面向的租户的名称。 |
| ElevationApprovedTime | 提升获得批准时的时间戳。 |
| ElevationApprover | Microsoft 管理器的名称。 |
| ElevationDuration | 提升处于活动状态的持续时间。 |
| ElevationRequestId |  提升请求的唯一标识符。 |
| ElevationRole | 为其请求提升的角色。 |
| ElevationTime | 提升的开始时间。 |
| Start_Time | cmdlet 执行的开始时间。 |


### <a name="exchange-admin"></a>Exchange 管理员
更改 Exchange 配置时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  指定 cmdlet 是由组织中的用户运行、由 Microsoft 数据中心人员或数据中心服务帐户运行，还是由委派的管理员运行。 值 False 标识 cmdlet 由组织中的某人运行。 值 True 表示 cmdlet 由数据中心人员、数据中心服务帐户或委派的管理员运行。 |
| ModifiedObjectResolvedName |  这是由 cmdlet 修改的对象的用户友好名称。 仅在 cmdlet 修改对象时才记录此信息。 |
| OrganizationName | 租户的名称。 |
| OriginatingServer | 从中执行 cmdlet 的服务器的名称。 |
| parameters | 与 Operations 属性中标识的 cmdlet 结合使用的所有参数的名称和值。 |


### <a name="exchange-mailbox"></a>Exchange 邮箱
更改 Exchange 邮箱或向其添加内容时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | 用于执行操作的电子邮件客户端的相关信息，例如浏览器版本、Outlook 版本和移动设备信息。 |
| Client_IPAddress | 记录操作时所用的设备的 IP 地址。 IP 地址以 IPv4 或 IPv6 地址格式显示。 |
| ClientMachineName | 托管 Outlook 客户端的计算机名称。 |
| ClientProcessName | 用于访问邮箱的电子邮件客户端。 |
| ClientVersion | 电子邮件客户端的版本。 |
| InternalLogonType | 保留供内部使用。 |
| Logon_Type | 表示访问邮箱并执行所记录的操作的用户类型。 |
| LogonUserDisplayName |    执行操作的用户的用户友好名称。 |
| LogonUserSid | 执行操作的用户的 SID。 |
| MailboxGuid | 所访问邮箱的 Exchange GUID。 |
| MailboxOwnerMasterAccountSid | 邮箱所有者帐户的主帐户 SID。 |
| MailboxOwnerSid | 邮箱所有者的 SID。 |
| MailboxOwnerUPN | 拥有所访问邮箱的人员的电子邮件地址。 |


### <a name="exchange-mailbox-audit"></a>Exchange 邮箱审核
创建邮箱审核项时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | 表示对其执行操作的项 | 
| SendAsUserMailboxGuid | 为发送电子邮件而访问的邮箱的 Exchange GUID。 |
| SendAsUserSmtp | 被模拟用户的 SMTP 地址。 |
| SendonBehalfOfUserMailboxGuid | 为代替发送邮件而访问的邮箱的 Exchange GUID。 |
| SendOnBehalfOfUserSmtp | 以其名义发送电子邮件的用户的 SMTP 地址。 |


### <a name="exchange-mailbox-audit-group"></a>Exchange 邮箱审核组
更改 Exchange 组或向其添加内容时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | 组中每个项的相关信息。 |
| CrossMailboxOperations | 表示操作是否涉及多个邮箱。 |
| DestMailboxId | 仅在 CrossMailboxOperations 参数为 True 时设置。 指定目标邮箱 GUID。 |
| DestMailboxOwnerMasterAccountSid | 仅在 CrossMailboxOperations 参数为 True 时设置。 为目标邮箱所有者的主帐户 SID 指定 SID。 |
| DestMailboxOwnerSid | 仅在 CrossMailboxOperations 参数为 True 时设置。 指定目标邮箱的 SID。 |
| DestMailboxOwnerUPN | 仅在 CrossMailboxOperations 参数为 True 时设置。 指定的目标邮箱所有者的 UPN。 |
| DestFolder | 针对“移动”等操作的目标文件夹。 |
| Folder | 一组项所在的文件夹。 |
| Folders |     操作中涉及的源文件夹相关信息；例如如果文件夹已选中且随后删除。 |


### <a name="sharepoint-base"></a>SharePoint Base
这些属性对于所有 SharePoint 记录通用。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | 确定事件在 SharePoint 中发生。 可能的值有 SharePoint 或 ObjectModel。 |
| ItemType | 访问或修改的对象的类型。 请参阅 ItemType 表，详细了解对象类型。 |
| MachineDomainInfo | 有关设备同步操作的信息。 仅在此信息出现在请求中时，才对其报告。 |
| MachineId |   有关设备同步操作的信息。 仅在此信息出现在请求中时，才对其报告。 |
| Site_ | 用户访问的文件或文件夹所在的站点的 GUID。 |
| Source_Name | 触发已审核操作的实体。 可能的值有 SharePoint 或 ObjectModel。 |
| UserAgent | 用户客户端或浏览器的相关信息。 此信息由客户端或浏览器提供。 |


### <a name="sharepoint-schema"></a>SharePoint 架构
对 SharePoint 进行配置更改时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | 自定义事件的可选字符串。 |
| Event_Data |  自定义事件的可选有效负载。 |
| ModifiedProperties | 包含此属性用于管理员事件，例如将用户添加为网站成员或网站集管理员组的成员。 该属性包括已修改的属性的名称（例如网站管理员组）、已修改属性的新值（例如已添加为网站管理员的用户），以及已修改的对象之前的值。 |


### <a name="sharepoint-file-operations"></a>SharePoint 文件操作
响应 SharePoint 中的文件操作时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | 复制或移动的文件的文件扩展名。 此属性仅对 FileCopied 和 FileMoved 事件显示。 |
| DestinationFileName | 复制或移动的文件的名称。 此属性仅对 FileCopied 和 FileMoved 事件显示。 |
| DestinationRelativeUrl | 复制或移动文件的目标文件夹的 URL。 SiteURL、DestinationRelativeURL 和 DestinationFileName 参数值的组合与 ObjectID 属性的值相同，即为已复制文件的完整路径名称。 此属性仅对 FileCopied 和 FileMoved 事件显示。 |
| SharingType | 分配给与其共享资源的用户的共享权限类型。 此用户由 UserSharedWith 参数标识。 |
| Site_Url | 用户访问的文件或文件夹所在的站点的 URL。 |
| SourceFileExtension | 用户访问的文件的文件扩展名。 如果所访问的对象是文件夹，则此属性空白。 |
| SourceFileName |  用户访问的文件或文件夹的名称。 |
| SourceRelativeUrl | 包含用户访问的文件的文件夹 URL。 SiteURL、SourceRelativeURL 和 SourceFileName 参数值的组合与 ObjectID 属性的值相同，即为用户访问的文件的完整路径名称。 |
| UserSharedWith |  与其共享资源的用户。 |




## <a name="sample-log-searches"></a>示例日志搜索
下表提供了此解决方案收集的更新记录的示例日志搜索。

| 查询 | 说明 |
| --- | --- |
|Office 365 订阅上所有操作的计数 |OfficeActivity &#124; summarize count() by Operation |
|SharePoint 网站的使用情况|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl | sort by Count asc|
|文件访问操作数（按用户类型）|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|使用特定关键字搜索|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|监视 Exchange 上的外部操作|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>后续步骤
* 使用[Log Analytics](../log-analytics/log-analytics-log-searches.md)中的日志搜索可查看详细的更新数据。
* [创建自己的仪表板](../log-analytics/log-analytics-dashboards.md)，显示最喜欢的 Office 365 搜索查询。
* [创建警报](../log-analytics/log-analytics-alerts.md)，主动接收重要的 Office 365 活动通知。  
