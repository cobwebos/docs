---
title: 教程：使用逻辑应用发送电子邮件
description: 了解如何从应用服务应用调用业务流程。 发送电子邮件、推文和 Facebook 帖子，添加到邮件列表等。
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 44f2dfb83e96e1d8fa31ee1acf350193b954dbd8
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892566"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>教程：从应用服务发送电子邮件及调用其他业务流程

本教程介绍如何将应用服务应用与业务流程相集成。 Web 应用方案经常使用此类操作，例如：

- 发送交易确认电子邮件
- 将用户添加到 Facebook 组
- 连接到 SAP、SalesForce 等第三方系统
- 交换标准的 B2B 消息

在本教程中，你将在应用服务应用中使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)通过 Gmail 发送电子邮件。 还可以通过其他方式（例如，语言框架提供的 SMTP 配置）从 Web 应用发送电子邮件。 但是，逻辑应用可为应用服务应用带来更多强大功能，且不会增大代码的复杂性。 逻辑应用为最常见的业务集成提供简单的配置界面，应用随时可以使用 HTTP 请求来调用它们。

## <a name="prerequisite"></a>先决条件

将采用所选语言框架的某个应用部署到应用服务。 若要遵循教程部署示例应用，请参阅：

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[教程：在 Azure 中生成 Node.js 和 MongoDB 应用](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[教程：在 Azure 中生成 PHP 和 MySQL 应用](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[教程：在 Azure 应用服务中使用 PostgreSQL 运行 Python (Django) Web 应用](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[在 Linux 上的 Azure 应用服务中生成 Ruby 和 Postgres 应用](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>创建逻辑应用

1. 在 [Azure 门户](https://portal.azure.com)中，按照[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)中的说明创建一个空逻辑应用。 看到“逻辑应用设计器”时，请返回本教程。 
1. 在逻辑应用设计器的初始页面中，选择“首先使用常用触发器”下面的“收到 HTTP 请求时”。  

    ![](./media/tutorial-send-email/receive-http-request.png)
1. 在“收到 HTTP 请求时”对话框中，选择“使用示例有效负载生成架构”。  

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. 将以下示例 JSON 复制到文本框中，然后选择“完成”。 

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    随即会为所需的请求数据生成架构。 在实践中，可以仅捕获应用程序代码生成的实际请求数据，并让 Azure 为你生成 JSON 架构。 
1. 在逻辑应用设计器的顶部，选择“保存”。  

    现在可以查看 HTTP 请求触发器的 URL。 选择“复制”图标以复制该 URL 供稍后使用。

    ![](./media/tutorial-send-email/http-request-url.png)

    此 HTTP 请求定义是要在此逻辑应用中执行的任何操作（通过 Gmail 发送电子邮件或其他任何操作）的触发器。 稍后你将在应用服务应用中调用此 URL。 有关请求触发器的详细信息，请参阅 [HTTP 请求/响应参考](../connectors/connectors-native-reqres.md)。

1. 在设计器的底部，单击“新建步骤”，在操作搜索框中键入 Gmail，然后找到并选择“发送电子邮件(V2)”。   
    
    > [!TIP]
    > 可以搜索其他类型的集成，例如 SendGrid、MailChimp、Office 365 和 SalesForce。 有关详细信息，请参阅[逻辑应用文档](https://docs.microsoft.com/azure/logic-apps/)。
1. 在“Gmail”对话框中选择“登录”，并登录到要从中发送电子邮件的 Gmail 帐户。  

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. 登录后，在“收件人”文本框中单击，此时会自动打开动态内容对话框。 

1. 在“收到 HTTP 请求时”操作的旁边，选择“查看更多”。  

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    现在，应会看到前面使用的示例 JSON 数据中的三个属性。 此步骤使用 HTTP 请求中的这些属性来构造电子邮件。
1. 由于你要为“收件人”字段选择值，因此请选择“电子邮件”。   如果需要，请单击“添加动态内容”关闭动态内容对话框。 

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. 在“添加新参数”下拉列表中，选择“主题”和“正文”。   

1. 在“主题”文本框中单击，以同样的方式选择“任务”。   在光标仍处于“主题”框中时，键入“已创建”。   

1. 在“正文”中单击，以同样的方式选择“到期时间”。   将光标移到“到期时间”的左侧，键入“此工作项的到期时间”。  

    > [!TIP]
    > 若要直接在电子邮件正文中编辑 HTML 内容，请选择逻辑应用设计器窗口顶部的“代码视图”。  只是要确保保留动态内容代码（例如 `@{triggerBody()?['due']}`）
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. 接下来，将异步 HTTP 响应添加到 HTTP 触发器。 在 HTTP 触发器与 Gmail 操作之间，单击“+”符号，然后选择“添加并行分支”。  

    ![](./media/tutorial-send-email/add-http-response.png)

1. 在搜索框中搜索“响应”，然后选择“响应”操作。  

    ![](./media/tutorial-send-email/choose-response-action.png)

    默认情况下，响应操作会发送 HTTP 200。 这对于本教程而言已足够。 有关详细信息，请参阅 [HTTP 请求/响应参考](../connectors/connectors-native-reqres.md)。

1. 在逻辑应用设计器的顶部，再次选择“保存”。  

## <a name="add-http-request-code-to-app"></a>将 HTTP 请求代码添加到应用

确保已复制前面看到的 HTTP 请求触发器 URL。 此 URL 包含敏感信息，因此最好不要直接将其放入代码中。 在应用服务中，可以使用应用设置将其作为环境变量进行引用。 

在 [Cloud Shell](https://shell.azure.com) 中，使用以下命令创建应用设置（请替换 \<app-name>、\<resource-group-name> 和 \<logic-app-url>）：   

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

在代码中，将适用于你的语言框架的任何 HTTP 客户端语言与以下配置配合使用，向该 URL 发出标准的 HTTP POST 请求：

- 请求正文包含提供给逻辑应用的相同 JSON 格式：

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- 请求包含标题 `Content-Type: application/json`。 
- 若要优化性能，在可能的情况下，请以异步方式发送请求。

单击下面的首选语言/框架选项卡查看示例。

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

在 ASP.NET 中，可以使用 [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) 类发送 HTTP POST 请求。 例如：

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

如果在[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)的示例应用中测试此代码，可以在添加 `Todo` 项后，使用该代码在 [Create 操作](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)中发送电子邮件确认。 若要使用上述异步代码，请将 Create 操作转换为异步操作。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

在 ASP.NET Core 中，可以使用 [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) 类发送 HTTP POST 请求。 例如：

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> 编写此代码是为了便于演示。 在实践中，请不要实例化每个请求的 `HttpClient` 对象。 请遵循 [使用 IHttpClientFactory 实现可复原的 HTTP 请求](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient -http-requests) 中的指导。

如果在[教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](app-service-web-tutorial-dotnetcore-sqldb.md)的示例应用中测试此代码，可以在添加 `Todo` 项后，使用该代码在 [Create 操作](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)中发送电子邮件确认。

# <a name="nodejs"></a>[Node.js](#tab/node)

在 Node.js 中，可以使用 [axios](https://www.npmjs.com/package/axios) 之类的 npm 包轻松发送 HTTP POST 请求。 例如：

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

如果在[教程：在 Azure 中生成 Node.js 和 MongoDB 应用](app-service-web-tutorial-nodejs-mongodb-app.md)的示例应用中测试此代码，可以在[成功保存项目](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)后，使用该代码在 [create 函数](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)中发送电子邮件确认。

# <a name="php"></a>[PHP](#tab/php)

在 PHP 中，可以使用 [Guzzle](http://docs.guzzlephp.org/en/stable/index.html) 轻松发送 HTTP POST 请求。 例如：

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

如果在[教程：在 Azure 中生成 PHP 和 MySQL 应用](app-service-web-tutorial-php-mysql.md)的示例应用中测试此代码，可以在紧靠在 return 语句前面的 [Route::post 函数](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)中使用该代码发送电子邮件确认。

# <a name="python"></a>[Python](#tab/python)

在 Python 中，可以使用 [requests](https://pypi.org/project/requests/) 轻松发送 HTTP POST 请求。 例如：

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

如果在[教程：在 Azure 应用服务中使用 PostgreSQL 运行 Python (Django) Web 应用](containers/tutorial-python-postgresql-app.md)的示例应用中测试此代码，可以在紧靠在 return 语句前面的 [Route::post 函数](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)中使用该代码发送电子邮件确认。

# <a name="ruby"></a>[Ruby](#tab/ruby)

在 Ruby 中，可以使用 [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient) 轻松发送 HTTP POST 请求。 例如：

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

如果在[在 Linux 上的 Azure 应用服务中生成 Ruby 和 Postgres 应用](containers/tutorial-ruby-postgres-app.md)的示例应用中测试此代码，可以[在 @task.save 成功后](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)，使用该代码在 [create](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) 操作中发送电子邮件确认。

---

# <a name="more-resources"></a>更多资源

[教程：在 Azure 应用服务中托管启用了 CORS 的 RESTful API](app-service-web-tutorial-rest-api.md)  
[逻辑应用的 HTTP 请求/响应参考](../connectors/connectors-native-reqres.md)  
[快速入门：使用 Azure 逻辑应用创建第一个工作流 - Azure 门户](../logic-apps/quickstart-create-first-logic-app-workflow.md)