---
title: "用于从 Azure Log Analytics 中检索数据的 Python 脚本 | Microsoft Docs"
description: "Log Analytics 日志搜索 API 允许任何 REST API 客户端从 Log Analytics 工作区中检索数据。  本文提供了使用日志搜索 API 的示例 Python 脚本。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 56d7c6dc648a01e7b0efc167cb65c94bac5468ec
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---

# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>使用 Python 脚本从 Log Analytics 中检索数据
[Log Analytics 日志搜索 API](log-analytics-log-search-api.md) 允许任何 REST API 客户端从 Log Analytics 工作区中检索数据。  本文提供了使用 Log Analytics 日志搜索 API 的示例 Python 脚本。  

## <a name="authentication"></a>身份验证
此脚本使用 Azure Active Directory 中的服务主体向工作区进行身份验证。  服务主体允许客户端应用程序请求服务对帐户进行身份验证，即使客户端没有帐户名称，也是如此。 在运行此脚本之前，必须使用[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)中所述的过程创建服务主体。  需要向脚本提供应用程序 ID、租户 ID 和身份验证密钥。 

> [!NOTE]
> [创建 Azure 自动化帐户](../automation/automation-create-standalone-account.md)时，将创建适合用于此脚本的服务主体。  如果已有 Azure 自动化创建的服务主体，则应能够使用它而不是创建一个新的，尽管可能需要[创建身份验证密钥](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)（如果还没有一个）。

## <a name="script"></a>脚本
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>后续步骤
- 详细了解 [Log Analytics 日志搜索 API](log-analytics-log-search-api.md)。
