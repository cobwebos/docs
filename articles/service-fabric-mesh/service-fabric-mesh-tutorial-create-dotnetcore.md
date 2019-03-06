---
title: 教程：创建、调试、监视多服务应用程序并将其部署到 Service Fabric 网格 | Microsoft Docs
description: 在本教程中，我们将创建一个多服务 Azure Service Fabric 网格应用程序（由可与后端 Web 服务通信的 ASP.NET Core 网站组成）、在本地调试该应用程序，然后将其发布到 Azure。
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2053706aac2e6136e35e8574dcd19150fe3d3b6a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805420"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>教程：创建、调试、部署和升级多服务 Service Fabric 网格应用

本教程是一个系列中的第一部分。 介绍了如何使用 Visual Studio 创建具有 ASP.NET web 前端和 ASP.NET Core Web API 后端服务的 Azure Service Fabric 网格应用。 然后，可在本地开发群集上调试应用。 还可将该应用发布到 Azure，然后进行配置和代码更改，并升级应用。 最后，可清理未使用的 Azure 资源，避免为没有使用的资源付费。

完成本教程后，可了解应用生命周期管理的大多数阶段，并生成了一个应用，该应用演示了 Service Fabric 网格应用中的服务到服务调用。

如果你不希望手动创建该待办事项应用程序，可以[下载已完成应用程序的源代码](https://github.com/azure-samples/service-fabric-mesh)，并跳到[在本地调试应用](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 使用 Visual Studio 创建包含 ASP.NET Web 前端的 Service Fabric 网格应用。
> * 创建一个用于表示待办事项的模型。
> * 创建后端服务并从中检索数据。
> * 将控制器和 DataContext 添加为后端服务的该模型视图控制器模式的一部分。
> * 创建一个用于显示待办事项的网页。
> * 创建用于标识后端服务的环境变量

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 在 Visual Studio 中创建 Service Fabric 网格应用
> * [调试在本地开发群集中运行的 Service Fabric 网格应用](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [部署 Service Fabric 网格应用](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [升级 Service Fabric 网格应用](service-fabric-mesh-tutorial-upgrade.md)
> * [清理 Service Fabric 网格资源](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 请确保已[设置开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)，包括安装 Service Fabric 运行时、SDK、Docker 和 Visual Studio 2017。

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>在 Visual Studio 中创建 Service Fabric 网格项目

运行 Visual Studio，并选择“文件” > “新建” > “项目...”。

在“新建项目”对话框顶部的“搜索”框中键入 `mesh`。 选择“Service Fabric 网格应用程序”模板。 （如果未看到该模板，请确保已根据[设置开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)中所述安装了网格 SDK 和 VS Tools 预览版。）  

在“名称”框中键入 `todolistapp`，在“位置”框中，将文件夹路径设置为要将项目文件存储到的位置。

确保已选中“创建解决方案的目录”，然后单击“确定”创建 Service Fabric 网格项目。

![Visual Studio 中的新建 Service Fabric 网格项目的对话框](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

随后会出现“新建 Service Fabric 服务”对话框。

### <a name="create-the-web-front-end-service"></a>创建 Web 前端服务

在“新建 Service Fabric 服务”对话框中选择“ASP.NET Core”项目类型，确保“容器 OS”设置为“Windows”。

将“服务名称”设置为 **WebFrontEnd**。 按“确定”创建 ASP.NET Core 服务。

![Visual Studio - 新建 Service Fabric 网格项目 - 对话框](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

随后会出现“ASP.NET Core Web 应用程序”对话框。 选择“Web 应用”，然后单击“确定”。

![Visual Studio - 新建 ASP.NET Core 应用程序](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

现已创建一个 Service Fabric 网格应用程序。 接下来，请创建待办事项信息的模型。

## <a name="create-the-to-do-items-model"></a>创建待办事项模型

为简单起见，待办事项已存储在内存中的列表中。 创建待办事项的类库，以及用于保存这些项的列表。 在 Visual Studio 中（当前已加载了 **todolistapp** 解决方案），选择“文件” > “添加” > “新建项目”。

在“添加新项目”对话框顶部的“搜索”框中键入 `C# .net core class`。 选择“类库(.NET Core)”模板。

在“名称”框中键入 `Model`。 单击“确定”创建类库。

在解决方案资源管理器中的“模型”下面，右键单击“Class1.cs”并选择“重命名”。 将类重命名为 **ToDoItem.cs**。 当出现提示，询问是否要重命名所有引用时，请单击“是”。

将空 `class ToDoItem` 的内容替换为：

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

此类表示待办事项。

在 Visual Studio 中，右键单击“模型”类库并选择“添加” > “类...”，以创建用于保存待办事项的列表。 此时会出现“添加新项”对话框。 将“名称”设置为 `ToDoList.cs`，然后单击“添加”。

在 **ToDoList.cs** 中，将空 `class ToDoList` 替换为：

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

接下来，创建用于跟踪待办事项的 Service Fabric 服务。

## <a name="create-the-back-end-service"></a>创建后端服务

在 Visual Studio 的“解决方案资源管理器”窗口中，右键单击“todolistapp”并单击“添加” > “新建 Service Fabric 服务...”

此时会出现“新建 Service Fabric 服务”对话框。 选择“ASP.NET Core”项目类型，确保“容器 OS”设置为“Windows”。 将“服务名称”设置为 **ToDoService**。 单击“确定”创建 ASP.NET Core 服务。

随后会出现“新建 ASP.NET Core Web 应用程序”对话框。 在该对话框中，依次选择“API”、“确定”，随即会将该服务的项目添加到解决方案。

![Visual Studio 中的“新建 ASP.NET Core 应用程序”](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

由于后端服务不提供任何 UI，因此，请禁用启动服务时启动浏览器的设置。 在“解决方案资源管理器”中，右键单击“ToDoService”并选择“属性”。 在显示的属性窗口中，选择左侧的“调试”选项卡，并取消选中“启动浏览器”。 按 **Ctrl+S** 保存更改。

由于此服务维护待办事项信息，因此请添加对“模型”类库的引用。 在解决方案资源管理器中，右键单击“ToDoService”并选择“添加” > “引用...”。此时会出现“引用管理器”对话框。

在“引用管理器”中，选中“模型”对应的复选框，然后单击“确定”。

### <a name="add-a-data-context"></a>添加数据上下文

接下来创建一个数据上下文，用于协调为数据模型中的数据提供服务的活动。

若要添加数据上下文类，请在解决方案资源管理器中，右键单击“ToDoService”并选择“添加” > “类”。
在显示的“添加新项”对话框中，确保已选中“类”，将“名称”设置为 `DataContext.cs`，然后单击“添加”。

在 **DataContext.cs** 中，将空 `class DataContext` 的内容替换为：

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

此精简数据上下文会填充一些示例待办事项，并提供对这些项的访问权限。

### <a name="add-a-controller"></a>添加控制器

在创建 **ToDoService** 项目时，模板已经提供了一个可以处理 HTTP 请求和创建 HTTP 响应的默认控制器。 在“解决方案资源管理器”中的“ToDoService”下面，打开 **Controllers** 文件夹查看 **ValuesController.cs** 文件。 

右键单击“ValuesController.cs”并选择“重命名”。 将此文件重命名为 `ToDoController.cs`。 如果出现提示，询问是否要重命名所有引用，请单击“是”。

打开 **ToDoController.cs** 文件，将 `class ToDoController` 的内容替换为：

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

本教程不会实现 add、delete 等方法，目的是将重点放在与另一服务的通信上。

## <a name="create-the-web-page-that-displays-to-do-items"></a>创建用于显示待办事项的网页

实现后端服务后，请编写用于显示该服务提供的待办事项的网站的代码。 以下步骤在 **WebFrontEnd** 项目中发生。

显示待办事项的网页需要有权访问 **ToDoItem** 类和列表。
在“解决方案资源管理器”中，右键单击“WebFrontEnd”并选择“添加” > “引用...”，添加对“模型”项目的引用此时会出现“引用管理器”对话框。

在“引用管理器”中，单击“模型”对应的复选框，然后单击“确定”。

在“解决方案资源管理器”中，导航到“WebFrontEnd” > “页面” > “Index.cshtml”，打开 Index.cshtml 页。 打开 **index.cshtml**。

将整个文件的内容替换为以下 HTML（定义一个简单的表用于显示待办事项）：

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

在“解决方案资源管理器”中单击 **Index.cshtml** 文件的下拉图标，然后打开 **Index.cshtml.cs**。

在 **Index.cshtml.cs** 的顶部添加 `using System.Net.Http;`

将 `public class IndexModel` 的内容替换为：

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>创建环境变量

需要使用后端服务的 URL 来与该服务通信。 在本教程中，以下代码摘录（前面已定义为 IndexModel 的一部分）读取环境变量以撰写 URL：

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

该 URL 由服务名称和端口组成。 在 **ToDoService** 项目的 service.yaml 文件中可以找到所有这些信息。

> [!IMPORTANT]
> 在以下步骤中，将修改 YAML 文件。
> 必须使用空格而不是制表符来缩进 service.yaml 文件中的变量，否则该文件无法编译。 当你创建环境变量时，Visual Studio 可能会插入制表符。 请将所有制表符替换为空格。 虽然在**生成**调试输出中会显示错误，但是应用仍然会启动，但它不会工作，除非你将制表符转换为空格并重新生成。 为确保 service.yaml 文件中不包含制表符，可以在 Visual Studio 编辑器中使用“编辑”  > “高级”  > “查看空白”来显示空白字符。
> 请注意，service.yaml 文件是使用英语区域设置进行处理的。 例如，如果需要使用小数分隔符，请使用句点而不是逗号。

在“解决方案资源管理器”中导航到“ToDoService”项目，并打开“服务资源” > “service.yaml”。

![图 1 - ToDoService service.yaml 文件](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

* 服务名称 `ToDoService` 位于 `services:` 下方。请参阅上图中的 (1)。
* 端口 `80` 位于 `endpoints:` 下方。请参阅上图中的 (2)。 你的项目的端口号可能与此不同。

接下来，需要在 WebFrontEnd 项目中定义用于表示服务名称和端口号的环境变量，使项目能够调用后端服务。

在“解决方案资源管理器”中，导航到“WebFrontEnd” > “服务资源” > “service.yaml”，定义用于指定后端服务地址的变量。

在 service.yaml 文件中，在 `environmentVariables:` 下添加以下变量（首先需要删除 `#` 以取消注释 `environmentVariables:`）。间距很重要，因此请将你添加的变量与 `environmentVariables:` 下的其他变量对齐。 ApiHostPort 的值应当与之前在 ToDoService 的 service.yaml 文件中看到的 ToDoServiceListener 的端口值匹配，这非常重要。

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> 有两种方法可以指定 `ToDoServiceName` 的值： 
> - 仅指定服务名称，在 Windows 10 上的调试场景中以及将服务部署到 Azure Service Fabric 网格时都会解析此名称。
> - 以 servicename.appname 形式完全限定。 这仅适用于在 Windows 10 上进行调试的场景。
> 较好的做法是仅使用服务名称进行服务解析。

**WebFrontEnd** 项目的 **service.yaml** 文件应大致如下所示，不过，你的 `ApiHostPort` 值可能与此不同：

![WebFrontEnd 项目中的 Service.yaml](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


现在，可以生成 Service Fabric 网格应用程序的映像以及后端 Web 服务，并将其部署到本地群集。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 创建包含 ASP.NET Web 前端的 Service Fabric 网格应用。
> * 创建一个用于表示待办事项的模型。
> * 创建后端服务并从中检索数据。
> * 将控制器和 DataContext 添加为后端服务的该模型视图控制器模式的一部分。
> * 创建一个用于显示待办事项的网页。
> * 创建用于标识后端服务的环境变量

转到下一教程：
> [!div class="nextstepaction"]
> [调试在本地开发群集中运行的 Service Fabric 网格应用程序](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)