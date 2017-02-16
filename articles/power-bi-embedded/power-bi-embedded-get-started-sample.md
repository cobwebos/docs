---
title: "示例入门"
description: "Power BI Embedded，使用 SDK 将交互式 Power BI 报表添加到商业智能应用程序"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2f0f36e7ffeec52bacc35ac5039cd183976dc3aa
ms.openlocfilehash: 9c9ab36ac90c3e73a21c7e53a6715cc4503a8e99


---
# <a name="get-started-with-power-bi-embedded-sample"></a>Power BI Embedded 示例入门
通过 **Microsoft Power BI Embedded**，可以将 Power BI 报表集成到 Web 或移动应用程序。 本文介绍了 **Power BI Embedded** 入门示例。

在继续之前，可能需要保存以下资源。 在将 Power BI 报表集成到示例应用和自己的应用中时，这些资源都可以提供帮助。

* [Web 应用示例仪表板](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI Embedded API 参考](https://msdn.microsoft.com/library/mt711493.aspx)
* [Power BI Embedded .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472)（通过 NuGet 提供）

> [!NOTE]
> 需要先在 Azure 订阅中创建至少一个**工作区集合**才能配置和运行 Power BI Embedded 入门示例。 若要了解如何在 Azure 门户中创建**工作区集合**，请参阅 [Power BI Embedded 入门](power-bi-embedded-get-started.md)。
>
>

## <a name="configure-the-sample-app"></a>配置示例应用
下面将引导完成 Visual Studio 开发环境的设置，以便访问运行示例应用时所需的组件。

1. 下载并解压缩 GitHub 上的 [Power BI Embedded - 将报表集成到 Web 应用中](http://go.microsoft.com/fwlink/?LinkId=761493)示例。
2. 在 Visual Studio 中打开 **PowerBI-embedded.sln**。 可能需要在 NuGET 程序包管理器控制台中执行 **Update-Package** 命令来更新此解决方案中使用的程序包。
3. 生成解决方案。
4. 运行 **ProvisionSample** 控制台应用。 在示例控制台应用中，预配一个工作区并导入 PBIX 文件。
5. 要预配新的**工作区**，请选择选项 5“在现有工作区集合中预配新的工作区”。

    ![](media/powerbi-embedded-get-started-sample/console-option-5.png)
6. 输入**工作区集合**名称和**访问密钥**。 可以通过 **Azure 门户**获取这些信息。 若要了解有关如何获取**访问密钥**的详细信息，请参阅“Microsoft Power BI Embedded 入门”中的[查看 Power BI API 访问密钥](power-bi-embedded-get-started.md#view-power-bi-api-access-keys)。

    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
7. 复制并保存新创建的**工作区 ID** 以便在本文后面部分使用。 创建**工作区 ID** 之后，可以在 **Azure 门户**中找到该数据。

    ![](media/powerbi-embedded-get-started-sample/workspace-id.png)
8. 若要将 PBIX 文件导入到**工作区**，请选择选项&6;**。**“将 PBIX 文件导入到现有工作区”。 如果没有现有的 PBIX 文件，则可以下载 [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547)（零售分析示例 PBIX）。
9. 如果出现提示，请为**数据集**输入一个易记的名称。

应该会看到如下所示的响应：

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> If your PBIX file contains any direct query connections, run option 7 to update the connection strings.
>
>

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app
The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [!NOTE]
> This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.
>
>

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

| Part | Description |
| --- | --- |
| Title |Name of the Report. |
| QueryString |A link to the Report ID. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller
**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrate a report into your app
Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-iframe-code.png)

## Filter reports embedded in your application
You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  
>
>

## See also
* [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
* [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)



<!--HONumber=Jan17_HO1-->


