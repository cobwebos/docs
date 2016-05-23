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
   ms.date="04/07/2016"
   wacn.date=""/>


# Azure Active Directory 报告 API 入门

本文档是 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)的一部分。

Azure Active Directory (AD) 提供各种活动、安全性与审核报告。该数据不仅可以通过 Azure 经典门户使用，在其他许多应用程序（例如 SIEM 系统、审核和商业智能工具）中也非常有用。

[Azure AD 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API（可从各种编程语言和工具调用），提供对这些数据的编程访问。

本文将引导你使用 PowerShell 完成调用 Azure AD 报告 API 的过程。你可以根据方案需求修改示例 PowerShell 脚本，以便从任何可用的报告（JSON、XML 或文本格式）访问数据。

若要使用本示例，你需要一个 [Azure Active Directory](active-directory-whatis.md) 租户。

## 创建 Azure AD 应用程序以访问 API

报告 API 使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授权访问 Web API。若要访问目录中的信息，必须在 Azure AD 租户中创建应用程序，并向其授予访问 Azure AD 数据的适当权限。


### 创建应用程序
- 导航到 [Azure 经典门户](https://manage.windowsazure.cn/)。
- 导航到 Azure AD 租户。
- 导航到“应用程序”选项卡。
- 在底部栏上，单击“添加”。
	- 单击“添加我的组织正在开发的应用程序”。
	- **名称**：任意名称即可。建议输入类似于“Reporting API Application”的内容。
	- **类型**：选择“Web 应用程序和/或 Web API”。
	- 单击箭头转到下一页。
	- **登录 URL**：```http://localhost```。
	- **应用 ID URI**：```http://localhost```。
	- 单击复选标记完成应用程序的添加。

### 授予应用程序使用 API 的权限
- 导航到“应用程序”选项卡。
- 导航到新建的应用程序。
- 单击“配置”选项卡。
- 在“针对其他应用程序的权限”部分中：
	- 在“Azure Active Directory”>“应用程序权限”中，选择“读取目录数据”。
- 在底部栏上，单击“保存”。


### 获取目录 ID、客户端 ID 和客户端机密

下列步骤将引导你获取应用程序的客户端 ID 和客户端密码。你也需要知道你的租户名称，它可以是 *.onmicrosoft.com 或自定义域名。将它们复制到不同的位置；你将使用它们来修改脚本。

#### 应用程序客户端 ID
- 导航到“应用程序”选项卡。
- 导航到新建的应用程序。
- 导航到“配置”选项卡。
- 应用程序的客户端 ID 列在“客户端 ID”字段中。

#### 应用程序客户端机密
- 导航到“应用程序”选项卡。
- 导航到新建的应用程序。
- 导航到“配置”选项卡。
- 通过在“密钥”部分中选择一个持续时间来为应用程序生成新密钥。
- 保存后，将显示该密钥。请确保将它复制并粘贴到安全的位置，因为以后无法检索它。


## 修改脚本
编辑下列其中一个脚本以搭配你的目录使用，方法是使用“在 Azure AD 中委托访问权限”中的正确值来替换 $ClientID、$ClientSecret 和 $tenantdomain。

### PowerShell 脚本

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

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

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


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
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## 执行脚本
完成脚本编辑后，运行该脚本并确认从 AuditEvents 报告返回预期的数据。

脚本会列出所有可用的报告，并在 PowerShell 窗口中从 AccountProvisioningEvents 报告返回 JSON 格式的输出。它也会创建具有相同输出的文件（JSON、文本和 XML 格式）。你可通过修改脚本以从其他报告返回数据来进行实验，并注释掉你不需要的输出格式。

## 说明

- Azure AD 报告 API（使用 OData 分页）返回的事件数目没有任何限制。
- 有关报告数据的保留限制，请查看[报告保留策略](active-directory-reporting-retention.md)。


## 后续步骤
- 想要知道可以使用哪些安全、审核和活动报告吗？ 查看 [Azure AD 安全、审核和活动报告](active-directory-view-access-usage-reports)
- 有关审核报告的详细信息，请参阅 [Azure AD 审核报告事件](active-directory-reporting-audit-events)
- 有关 Azure AD 图形 API REST 服务的更多详细信息，请参阅 [Azure AD 报告和事件（预览版）](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)

<!---HONumber=Mooncake_0516_2016-->