---
title: "测试 Azure Functions | Microsoft Docs"
description: "使用 Postman、cURL 和 Node.js 测试 Azure Functions。"
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，webhook，动态计算，无服务体系结构，测试"
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 3603f58a9df1f0222a75b863ad2c1ab1b6e13fb2
ms.openlocfilehash: c6868566e513c5cd2c76be3305ca6c9035d58acd


---
# <a name="testing-azure-functions"></a>测试 Azure Functions
## <a name="overview"></a>概述
本主题演示各种测试函数的方法，其中包括以下常规方法：

+ 基于 HTTP 的工具，如 cURL、Postman，甚至用作基于 Web 的触发器的 Web 浏览器。 
+ 用于测试基于 Azure 存储的触发器的存储资源管理器。
+ Functions 门户中的“测试”选项卡。
+ 计时器触发的函数。
+ 测试应用程序或框架。  

上述所有测试方法均使用一个 HTTP 触发器函数，该函数可通过查询字符串参数或请求正文接受输入。 你将在第一部分中创建此函数。

## <a name="create-a-function-for-testing"></a>创建测试函数
此教程中大多使用经过略微修改的 HttpTrigger JavaScript 函数模板版本，新建函数时可使用该模板版本。  新建函数时需要帮助，可查看[创建你的第一个 Azure 函数](functions-create-first-azure-function.md)。  在 [Azure 门户]中创建测试函数时只需选择 **HttpTrigger- JavaScript** 模板。

默认函数模型基本为 hello world 函数，可从请求正文或查询字符串 `name=<your name>` 回显名称。  我们将更新代码，让你也能提供名称和代码作为请求正文中的 JSON 内容。 然后该函数会在可能的时候将这些回显到客户端。   

使用下方代码更新函数，测试将会用到这些代码：

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>使用工具测试函数
除了 Azure 门户之外，还有各种可用于触发函数进行测试的工具。 其中包括 HTTP 测试工具（基于 UI 的工具和命令行）、Azure 存储访问工具，甚至简单的 Web 浏览器。

### <a name="test-with-a-browser"></a>使用浏览器测试
Web 浏览器是通过 HTTP 触发器函数的简单方法。 可以对不需要正文负载而仅使用查询字符串参数的 GET 请求使用浏览器。

若要测试上方定义的函数，请从门户复制**函数 URL**。 格式应如下：

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

使用 `<Enter a name here>` 占位符的实际名称将 `name` 参数追加到查询字符串。 

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

将 URL 粘贴到浏览器，会获得与下方类似的想用。。

![](./media/functions-test-a-function/browser-test.png)

此示例是 Chrome 浏览器，它以 XML 格式包装返回的字符串。 其他浏览器只显示字符串值。

在门户的“日志”窗口中，执行函数时会记录与下方类似的输出：

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>使用 Postman 测试
推荐使用与 Chrome 浏览器集成的 Postman 来测试大多数函数。 若要安装 Postman，请参阅[获取 Postman](https://www.getpostman.com/)。 Postman 可控制更多 HTTP 请求的属性。

> [!TIP]
> 使用你最熟悉的 HTTP 测试工具。 下面是集中替代 Postman 的方案：  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  
>
>

在 Postman 中测试具有请求正文的函数：

1. 从 Chrome 浏览器窗口左上角的“应用”按钮启动 Postman。
2. 复制**函数 URL** 并将其粘贴到 Postman。 其中包括访问代码查询字符串参数。
3. 将 HTTP 方法更改为 **POST**。
4. 单击“正文” > “原始”，添加类似于下方的 JSON 请求正文：
    
    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, W.A. 98101"
    }
    ```
5. 单击“发送”。

下图显示了本教程中测试简单回显函数的示例。

![](./media/functions-test-a-function/postman-test.png)

在门户的“日志”窗口中，执行函数时会记录与下方类似的输出：

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>从命令行使用 cURL 测试 
测试软件时往往只需查看命令行来帮助你调试应用程序，这与函数相同。 请注意，默认情况下，cURL 在基于 Linux 的系统上可用。 在 Windows 上，必须先下载并安装 [cURL 工具](https://curl.haxx.se/)。 

若要测试上述函数，请从门户复制**函数 URL**。 格式应如下：

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

此 URL 用于触发函数，可在命令行上使用 cURL 命令对其进行测试，发出针对函数的 GET（`-G` 或 `--get`）请求：

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

上方的特定示例需要查询字符串参数，可在 cURL 命令行中将参数作为数据 (`-d`) 传送：

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

运行命令并在命令行上查看该函数的以下输出：

![](./media/functions-test-a-function/curl-test.png)

在门户的“日志”窗口中，执行函数时会记录与下方类似的输出：

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-using-storage-explorer"></a>使用存储浏览器测试 blob 触发器
可使用 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)测试 blob 触发器函数。

1. 在 Function App 的 [Azure 门户]中，新建一个 C#、F# 或 JavaScript blob 触发器函数。 将要监视路径设为 blob 容器名。 例如：

        files
2. 单击 **+** 按钮选择或创建需要使用的存储帐户。 然后单击“创建” 。
3. 使用如下文本创建一个测试文件并保存：

        A text file for blob trigger function testing.
4. 运行 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/) 并连接到存储帐户中受监视的 blob 容器。
5. 单击“上传”按钮，上传该测试文件。

    ![](./media/functions-test-a-function/azure-storage-explorer-test.png)

    默认 blob 触发器函数代码将在日志中报告 blob 的处理过程：

        2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
        2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
        2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
        2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>在函数内部测试函数
Azure Functions 门户旨在让你测试 HTTP 和计时器触发的函数。 还可以创建函数以触发其他要测试的函数。

### <a name="test-with-the-functions-portal-run-button"></a>使用 Functions 门户运行按钮进行测试
通过门户提供的“运行”按钮可完成一些有限的测试。 可使用“运行”按钮提供请求正文，但无法提供查询字符串参数或更新请求标题。

在**请求正文**中添加与下方类似的 JSON 字符串测试之前创建的 HTTP 触发器函数，然后单击“运行”按钮。

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

在门户的“日志”窗口中，执行函数时会记录与下方类似的输出：

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>使用计时器触发器进行测试
某些函数无法通过上述工具进行真正的测试。 例如，队列触发器函数，该函数会在信息放到 [Azure 队列存储](../storage/storage-dotnet-how-to-use-queues.md)时运行。 可以随时编写代码，将信息放到队列中，下方提供了在控制台项目中进行此操作的示例。 但是，还有一种可直接使用函数进行测试的方法。  

可使用配置有队列输出绑定的计时器触发器。 该计时器触发器代码随后可将测试信息写入队列中。 此部分将通过示例进行说明。

获取有关使用 Azure Functions 绑定的深入信息，请参阅 [Azure Functions 研发人员参考](functions-reference.md)。

#### <a name="create-queue-trigger-for-testing"></a>创建用于测试的队列触发器
为了演示这种方法，首先会创建一个队列触发器函数，我们希望对函数的 `queue-newusers` 队列进行测试。 此函数会处理放到 Azure 队列存储的新用户的名称和地址信息。

> [!NOTE]
> 如果使用不同的队列名称，请确保使用的名称符合[命名队列和元数据](https://msdn.microsoft.com/library/dd179349.aspx)规则。  否则，会出现 HTTP 状态代码 400：错误请求。
>
>

1. 在 Function App 的 [Azure 门户]中，单击“新建函数” > “队列触发器 - C#”。
2. 输入队列函数要监视的队列名

        queue-newusers
3. 单击 **+**（添加）按钮选择或创建需要使用的存储帐户。 然后单击“创建” 。
4. 将此门户浏览器窗口保持在开启状态，即可监视默认队列函数模板代码的日志项。

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>创建计时器触发器，将消息放入队列
1. 在新的浏览器窗口打开 [Azure 门户]并导航到 Function App。
2. 单击“新建函数” > “计时器触发器 - C#”。 输入 cron 表达式，设置计时器代码测试队列函数的频率。 然后单击“创建” 。 如果希望每 30 秒检查一次，了使用以下 [CRON 表达式](https://wikipedia.org/wiki/Cron#CRON_expression)：

        */30 * * * * *
3. 单击新计时器触发器的“集成”选项卡。
4. 在下“输出”下单击“+ 新输出”按钮。 然后单击“队列”和“选择”按钮。
5. 记下**队列信息对象**所用的名称，计时器函数代码中会用到该名称。

        myQueue
6. 输入要发送信息的队列名称：

        queue-newusers
7. 单击 **+**（添加）按钮，选择队列触发器以前使用的存储帐户。 然后单击“保存”。
8. 单击计时器触发器的“开发”选项卡。
9. 只要使用了如上所示的相同的队列信息对象名称，就可以为 C# 计时器函数使用如下代码。 然后单击“保存”

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

如果使用了示例 cron 表达式，此时 C# 计时器函数将每隔 30 秒运行一次。 计时器函数日志会报告每一次执行：

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

在队列函数浏览器窗口中，会出现处理的每一个信息：

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>使用代码测试函数
在某些情况下，你将需要创建外部应用程序或框架以测试函数。

### <a name="test-a-http-trigger-function-with-code-nodejs"></a>使用代码测试 HTTP 触发器函数：Node.js
可使用 Node.js 应用执行 HTTP 请求以测试函数。
请确保设置以下内容：

* 将要求选中的 `host` 设置为 Function App 主机
* 在 `path` 中设置函数名。
* 在 `path` 中设置访问代码 (`<your code>`)。

代码示例：

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


输出：

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

在门户的“日志”窗口中，执行函数时会记录与下方类似的输出：

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>使用代码测试队列触发器函数：C## #
之前有提到，可以使用代码将信息放到队列中，测试队列触发器。 下方代码示例以 [Azure 队列存储入门](../storage/storage-dotnet-how-to-use-queues.md)教程中提供的 C# 代码为基础。 链接还提供了其他语言的代码。

若要在控制台应用中测试此代码，必须：

* [在 app.config 文件中配置存储连接字符串](../storage/storage-dotnet-how-to-use-queues.md)。
* 运行时，此代码会接受新用户的名称和地址作为命令行参数。 将 `name` 和 `address` 作为参数传送到应用。 例如 `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`

示例 C# 代码：

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

在队列函数浏览器窗口中，会出现处理的每一个信息：

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure 门户]: https://portal.azure.com



<!--HONumber=Feb17_HO1-->


