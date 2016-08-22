<properties
   pageTitle="Azure AD 报告 API 入门 | Microsoft Azure"
   description="如何开始使用 Azure Active Directory Reporting API"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>  


<tags
   ms.service="active-directory"
   ms.date="06/28/2016"
   wacn.date=""/>  



# Azure Active Directory 报告 API 入门

本文档是 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)的一部分。

Azure Active Directory (AD) 提供各种活动、安全性与审核报告。该数据不仅可以通过 Azure 经典门户使用，在其他许多应用程序（例如 SIEM 系统、审核和商业智能工具）中也非常有用。

[Azure AD 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API（可从各种编程语言和工具调用），提供对这些数据的编程访问。

本文将引导你使用 PowerShell 完成调用 Azure AD 报告 API 的过程。你可以根据方案需求修改示例 PowerShell 脚本，以便从任何可用的报告（JSON、XML 或文本格式）访问数据。

若要使用本示例，你需要一个 [Azure Active Directory](/documentation/articles/active-directory-whatis/) 租户。

## 创建 Azure AD 应用程序以访问 API

报告 API 使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授权访问 Web API。若要访问目录中的信息，必须在 Azure AD 租户中创建应用程序，并向其授予访问 Azure AD 数据的适当权限。


### 注册 Azure AD 应用程序
若要完成 Azure AD 应用程序注册工作，必须使用同为 Azure AD 租户中“全局管理员”目录角色成员的 Azure 订阅管理员帐户登录到 Azure 经典门户。这是因为你将使用一定的权限来注册 Azure AD 应用程序，而此权限需要使用具有全局管理员权限的帐户注册/同意。

> [AZURE.IMPORTANT] 在具有此类“管理”特权的凭据下运行的应用程序可以具有非常强大的功能，因此请务必保证应用程序 ID/机密凭据的安全。


1. 导航到 [Azure 经典门户](https://manage.windowsazure.cn/)。
2. 在左窗格中下方的“Active Directory”扩展上，导航到你的 Azure AD 租户中。
3. 导航到“应用程序”选项卡。
4. 在底部栏上，单击“添加”。
	- 单击“添加我的组织正在开发的应用程序”。
	- **名称**：任意名称即可。建议输入类似于“Reporting API Application”的内容。
	- **类型**：选择“Web 应用程序和/或 Web API”。
	- 单击箭头转到下一页。
	- **登录 URL**：`https://localhost`。
	- **应用 ID URI**：`https://localhost/ReportingApiApp`
	- 单击复选标记完成应用程序的添加。

### 授予应用程序使用 API 的权限
1. 导航到“应用程序”选项卡。
2. 导航到新建的应用程序。
3. 单击“配置”选项卡。
4. 在“针对其他应用程序的权限”部分中：
	- 对于“Microsoft Azure Active Directory”资源（对 Azure AD 图形 API 的权限），请单击“应用程序权限”下拉列表，并选择“读取目录数据”。

        > [AZURE.IMPORTANT] 请务必在此指定正确的权限。应用“应用程序权限”而非“委派权限”，否则为应用程序将不会获得访问 Reporting API 所需的权限级别，且你的脚本将接收到“无法检查 appId 的目录读取访问权限”错误。


5. 在底部栏上，单击“保存”。


### 获取目录 ID、客户端 ID 和客户端机密

下列步骤将引导你获取应用程序的客户端 ID 和客户端密码。你也需要知道你的租户名称，它可以是 *.onmicrosoft.com 或自定义域名。将它们复制到不同的位置；你将使用它们来修改脚本。

#### 获取 Azure AD 租户的域名
1. 在左窗格中下方的“Active Directory”扩展上，导航到你的 Azure AD 租户中。
2. 导航到“域”选项卡。
4. 将不显示租户的“<租户名称>.onmicrosoft.com”域名，也不会显示任何已配置的自定义域名。

#### 获取应用程序的客户端 ID
1. 导航到“应用程序”选项卡。
2. 导航到新建的应用程序。
3. 导航到“配置”选项卡。
4. 应用程序的客户端 ID 列在“客户端 ID”字段中。

#### 获取应用程序的客户端密码
1. 导航到“应用程序”选项卡。
2. 导航到新建的应用程序。
3. 导航到“配置”选项卡。
4. 通过在“密钥”部分中选择一个持续时间来为应用程序生成新密钥。
5. 保存后，将显示该密钥。请确保将它复制并粘贴到安全的位置，因为以后无法检索它。


## 修改脚本
编辑下列其中一个脚本以搭配你的目录使用，方法是使用“在 Azure AD 中委托访问权限”中的正确值来替换 $ClientID、$ClientSecret 和 $tenantdomain。

### PowerShell 脚本
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/reports/auditEvents?api-version=beta&$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

### Bash 脚本

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## 执行脚本
完成脚本编辑后，运行该脚本并确认从 AuditEvents 报告返回预期的数据。

此脚本以 JSON 格式从 auditEvents 报表返回输出。它还将创建具有相同输出的 `auditEvents.json` 文件。你可通过修改脚本以从其他报告返回数据来进行实验，并注释掉你不需要的输出格式。

## 说明

- Azure AD 报告 API（使用 OData 分页）返回的事件数目没有任何限制。
- 有关报告数据的保留限制，请查看[报告保留策略](/documentation/articles/active-directory-reporting-retention/)。


## 后续步骤
- 想要知道可以使用哪些安全、审核和活动报告吗？ 查看 [Azure AD 安全、审核和活动报告](/documentation/articles/active-directory-view-access-usage-reports/)
- 有关审核报告的详细信息，请参阅 [Azure AD 审核报告事件](/documentation/articles/active-directory-reporting-audit-events/)
- 有关 Azure AD 图形 API REST 服务的更多详细信息，请参阅 [Azure AD 报告和事件（预览版）](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)

<!---HONumber=Mooncake_0815_2016-->