---
title: 教程 - 升级 Azure Service Fabric 网格应用程序 | Microsoft Docs
description: 了解如何使用 Visual Studio 升级 Service Fabric 应用程序
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: c23646bca6109d27e57b2f928363e65c83c634eb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031146"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>教程：了解如何使用 Visual Studio 升级 Service Fabric 应用程序

本教程是一个系列的第四部分，介绍如何直接从 Visual Studio 升级 Azure Service Fabric 网格应用程序。 升级包括代码更新和配置更新。 你将看到在 Visual Studio 中升级和发布的步骤是相同的。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 使用 Visual Studio 升级 Service Fabric 网格服务

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [在 Visual Studio 中创建 Service Fabric 网格应用](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [调试在本地开发群集中运行的 Service Fabric 网格应用](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [部署 Service Fabric 网格应用](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * 升级 Service Fabric 网格应用
> * [清理 Service Fabric 网格资源](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果尚未部署待办事项应用，请按照[发布 Service Fabric 网格 Web 应用程序](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)中的说明操作。

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>使用 Visual Studio 升级 Service Fabric 网格服务

本文介绍如何在应用程序中独立升级微服务。  在本示例中，我们将修改 `WebFrontEnd` 服务以显示任务类别。 然后，升级已部署的服务。

## <a name="modify-the-config"></a>修改配置

升级可能是由于代码更改、配置更改或两者都有。  要引入配置更改，请打开 `WebFrontEnd` 项目的 `service.yaml` 文件（具体位于“服务资源”节点下）。

在 `resources:` 部分中，将 `cpu:` 从 0.5 更改为 1.0，预计 Web 前端使用量较大。 它现在应如下所示：

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>修改模型

要引入代码更改，请将 `Category` 属性添加到 `ToDoItem.cs` 文件中的 `ToDoItem` 类。

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

然后更新 `Load()` 方法，在同一文件中，将类别设置为默认字符串：

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>修改服务

`WebFrontEnd` 项目是一个 ASP.NET Core 应用程序，其中包含一个显示待办事项列表项的网页。 在 `WebFrontEnd` 项目中，打开 `Index.cshtml` 并添加以下两行（如下所示），显示任务的类别：

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

生成并运行应用，验证是否在网页中看到列出任务的新类别列。

## <a name="upgrade-the-app-from-visual-studio"></a>从 Visual Studio 升级应用

无论是进行代码升级还是配置升级（在本例中我们同时执行这两者），要在 Azure 上升级 Service Fabric 网格应用，请在 Visual Studio 中右键单击“todolistapp”，然后选择“发布...”

接下来，查看“发布 Service Fabric 应用程序”对话框。

![Visual Studio - Service Fabric 网格 - 发布对话框](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

选择自己的 Azure 帐户和订阅。 确保将“位置”设置为最初将待办事项应用发布到 Azure 时使用的位置。 本文使用“美国东部”。

确保将“资源组”设置为最初将待办事项应用发布到 Azure 时使用的资源组。

确保将“Azure 容器注册表”设置为最初将待办事项应用发布到 Azure 时创建的 Azure 容器注册表名称。

在“发布”对话框中，按“发布”按钮升级 Azure 上的待办事项应用。

可以通过在 Visual Studio 的“输出”窗口中选择“Service Fabric 工具”窗格，来监视升级的进度。 完成升级后，“Service Fabric 工具”输出将以 URL 的形式显示应用程序的 IP 地址和端口。

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

打开 Web 浏览器并导航到该 URL，以查看 Azure 中运行的网站。 现在应看到一个网页，其中包含一个类别列。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了：
> [!div class="checklist"]
> * 如何使用 Visual Studio 升级 Service Fabric 网格应用

转到下一教程：
> [!div class="nextstepaction"]
> [清理 Service Fabric 网格资源](service-fabric-mesh-tutorial-cleanup-resources.md)