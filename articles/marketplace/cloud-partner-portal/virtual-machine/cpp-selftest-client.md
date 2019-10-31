---
title: 用于预验证虚拟机的自我测试客户端 |Azure Marketplace
description: 如何为 Azure 市场创建用来预验证虚拟机映像的自测试客户端。
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pabutler
ms.openlocfilehash: 46923ecd33a054a36aa6900a415d0b563e5afff0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163254"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>创建用来预验证 Azure 虚拟机映像的自测试客户端

本文可用作创建使用自测试 API 的客户端服务的指南。 可使用自测试 API 来预验证虚拟机 (VM)，以确保其满足最新的 Azure 市场发布要求。 此客户端服务使你可以在提交 Microsoft 认证的产品/服务之前测试 VM。

## <a name="development-and-testing-overview"></a>开发和测试概述

作为自测试过程的一部分，将创建连接到 Azure 市场的本地客户端，以验证在 Azure 订阅中运行的 VM。 VM 可以运行 Windows 或 Linux 操作系统。

本地客户端运行使用自测试 API 进行身份验证、发送连接信息以及接收测试结果的脚本。

创建自测试客户端的高级步骤如下：

1. 为应用程序选择 Azure Active Directory (AD) 租户。
2. 注册客户端应用。
3. 为客户端 Azure AD 应用创建令牌。
4. 将令牌传递给自测试 API。

创建客户端后，可以针对 VM 进行测试。

### <a name="self-test-client-authorization"></a>自测试客户端授权

下图显示了授权如何使用客户端凭据（共享密钥或证书）为服务调用服务。

![客户端授权过程](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>自测试客户端 API

自测试 API 包含仅支持 POST 方法的单个终结点。  它具有以下结构。

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

下表描述了 API 各字段。


|      字段         |    描述    |
|  ---------------   |  ---------------  |
|  授权     |  “持有者 xxxx-xxxx-xxxx-xxxxx”字符串包含可通过使用 PowerShell 创建的 Azure Active Directory (AD) 客户端令牌。          |
|  DNSName           |  要测试的 VM 的 DNS 名称    |
|  用户              |  用于登录到 VM 的用户名         |
|  密码          |  用于登录到 VM 的密码          |
|  OS                |  VM 的操作系统：`Linux` 或 `Windows`          |
|  PortNo            |  用于连接到 VM 的开放端口号。 通常，对于 Linux，端口号为 `22`，对于 Windows，端口号为 `5986`。          |
|  |  |

## <a name="consuming-the-api"></a>使用 API

可通过 PowerShell 或 cURL 使用自测试 API。

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>通过 PowerShell 使用 Linux OS 上的 API

要在 PowerShell 中调用 API，请执行以下步骤：

1. 使用 `Invoke-WebRequest` 命令以调用 API。
2. 方法是 Post，内容类型是 JSON，如以下代码示例和屏幕截图所示。
3. 采用 JSON 格式指定正文参数。

以下代码示例显示了对 API 的 PowerShell 调用。

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
以下屏幕截图显示了在 PowerShell 中调用 API 的示例。

![Linux OS 使用 PowerShell 调用 API](./media/stclient-call-api-ps-linuxvm.png)

使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

以下屏幕截图显示 `$res.Content`，采用 JSON 格式提供测试结果的详细信息。

![来自 PowerShell 调用 Linux 的 JSON 结果](./media/stclient-pslinux-rescontent-json.png)

以下屏幕截图显示了在联机 JSON 查看器（例如，[Code Beautify](https://codebeautify.org/jsonviewer) 或 [JSON Viewer](https://jsonformatter.org/json-viewer)）中查看的 JSON 测试结果的示例。

![来自 PowerShell 调用 Linux VM 的 JSON 结果](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>通过 PowerShell 使用 Windows OS 上的 API

要在 PowerShell 中调用 API，请执行以下步骤：

1. 使用 `Invoke-WebRequest` 命令以调用 API。
2. 方法是 Post，内容类型是 JSON，如以下代码示例和屏幕截图所示。
3. 创建采用 JSON 格式的正文参数。

以下代码示例显示了对 API 的 PowerShell 调用。

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

以下屏幕截图显示了在 PowerShell 中调用 API 的示例。

![Windows VM 使用 PowerShell 调用 API](./media/stclient-call-api-ps-windowsvm.png)

使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

以下屏幕截图显示 `$res.Content`，采用 JSON 格式提供测试结果的详细信息。

![来自 PowerShell 调用 Windows 的 JSON 结果](./media/stclient-pswindows-rescontent-json.png)

以下屏幕截图显示了在联机 JSON 查看器中查看的测试结果。
（例如，[Code Beautify](https://codebeautify.org/jsonviewer)、[JSON 查看器](https://jsonformatter.org/json-viewer)）

![来自 PowerShell 调用 Windows VM 的 JSON 结果](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>通过 cURL 使用 Linux OS 上的 API

若要使用 cURL 调用 API，请执行以下步骤：

1. 使用 curl 命令调用 API。
2. 方法是 Post，内容类型是 JSON，如以下代码片段示例所示。

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

以下屏幕显示使用 curl 调用 API 的示例。

![使用 curl 命令调用 API](./media/stclient-consume-api-curl.png)

以下屏幕截图显示了来自 curl 调用的 JSON 结果。

![来自 curl 调用的 JSON 结果](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>为应用选择 Azure AD 租户

使用以下步骤选择要在其中创建应用程序的 Azure AD 租户。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在顶部菜单栏上，选择你的帐户，然后在“目录”列表下，选择要在其中注册应用程序的 Active Directory 域服务租户。 也可选择“目录 + 订阅”图标以查看全局订阅筛选器。 以下屏幕截图显示了此筛选器的示例。

   ![选择订阅筛选器](./media/stclient-subscription-filter.png)

3. 在左侧导航栏中，选择“所有服务”，然后选择“Azure Active Directory”。

   在以下步骤中，可能需要提供租户名称（或目录名称）或租户 ID（或目录 ID）。

   **获取租户信息：**

   在“Azure Active Directory”概述中，搜索“属性”，然后选择“属性”。 使用以下屏幕截图为例：

   - **名称** - 租户名称或目录名称
   - **目录 ID** - 租户 ID 或目录 ID，或使用滚动条查找“属性”。

   ![Azure Active Directory 属性页](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>注册客户端应用

要注册客户端应用，请使用以下步骤进行操作。

1. 在左侧导航栏中，选择“所有服务”，然后选择“应用注册”。
2. 在“应用注册”下，选择“+ 新建应用程序注册”。
3. 在“创建”下，提供以下字段所需的信息：

   - **名称** - 输入应用的友好名称。 例如，“SelfTestClient”。
   - **应用程序类型** - 选择“Web 应用/API”
   - "**登录 URL** "-键入 "https：\//isvapp.azurewebsites.net/selftest-vm"

4. 选择**创建**。
5. 在“应用注册”或“已注册的应用”下，复制“应用程序 ID”。

   ![获取应用程序 ID](./media/stclient-app-id.png)

6. 在已注册的应用工具栏中，选择“设置”。
7. 选择“所需权限”以为应用程序配置权限。
8. 在“所需权限”下，选择“+ 添加”。
9. 在“添加 API 访问权限”下，选取“选择 API”。
10. 在“选择 API”下，键入“Microsoft Azure 经典部署模型”以搜索 API。
11. 在搜索结果中，选取“Microsoft Azure 经典部署模型”，然后单击“选择”。

    ![为应用配置多租户](./media/stclient-select-api.png)

12. 在“添加 API 访问权限”下，选取“选择权限”。
13. 选择“访问 Microsoft Azure 服务管理 API”。

    ![为应用启用 API 访问权限](./media/stclient-enable-api-access.png)

14. 单击“选择”。
15. 选择“完成”。
16. 在“设置”下，选择“属性”。
17. 在“属性”下，向下滚动到“多租户”。 请选择“是”。

    ![为应用配置多租户](./media/stclient-yes-multitenant.png)

18. 选择“保存”。
19. 在“设置”下，选择“密钥”。
20. 选择密钥“说明”文本框，创建密钥。 配置以下字段：

    - 键入密钥名称。 例如，“selftestclient”
    - 在“到期时间”下拉列表中选择“1 年”。
    - 选择“保存”以生成密钥。
    - 在“值”下，复制该密钥。

      >[!Important]
      >退出“密钥”表单后，将无法看到密钥值。

    ![密钥值表单](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>为客户端应用创建令牌

可使用以下任意程序通过 OAuth REST API 来创建和获取令牌：

- Postman
- Linux 中的 cURL
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>使用 Postman 创建和获取令牌

 要向 Auth0 请求任何授权的应用程序的令牌，请使用以下格式的有效负载对 [https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) 终结点执行 POST 操作：

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

在请求正文中传递以下参数：

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

在请求标头中传递以下参数：

```
Content-Type: application/x-www-form-urlencoded
```

以下屏幕截图显示了使用 Postman 获取令牌的示例。

![使用 Postman 获取令牌](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>在 Linux 中使用 cURL 创建和获取令牌

要向 Auth0 请求任何授权的应用程序的令牌，请使用以下格式的有效负载对 [https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) 终结点执行 POST 操作：

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

以下屏幕截图显示了使用 curl 命令获取令牌的示例。

![使用 curl 命令获取令牌](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>使用 C&#35;创建和获取令牌

若要向 Auth0 请求任何已授权应用程序的令牌，请使用以下格式的有效负载对 https：\//soamtenant.auth0.com/oauth/token 终结点执行 POST 操作：

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>使用 PowerShell 创建和获取令牌

若要向 Auth0 请求任何已授权应用程序的令牌，请使用以下格式的有效负载对 https：\//soamtenant.auth0.com/oauth/token 终结点执行 POST 操作：

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>将客户端应用令牌传递给 API

使用授权标头中的以下代码将令牌传递给自测试 API：

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>测试自测试客户端

要测试客户端，请执行下列步骤：

1. 部署要测试的 VM。
2. 使用客户端应用令牌调用自测试 API 进行授权。
3. 获取 JSON 格式的测试结果。

### <a name="test-result-examples"></a>测试结果示例

以下代码片段显示 JSON 格式的测试结果。

**Windows VM 的测试结果：**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Linux VM 的测试结果：**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>后续步骤

成功测试 Azure 虚拟机后，可以[发布产品/服务](./cpp-publish-offer.md)。
