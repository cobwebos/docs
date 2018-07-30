---
title: 教程 - 调试 Azure Service Fabric 网格 Web 应用程序 | Microsoft Docs
description: 本教程介绍如何调试本地群集上运行的 Azure Service Fabric 网格应用程序。
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: ad6812f25ee33bf723ed86d4ec32ca6898d01774
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186732"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>教程：调试 Service Fabric 网格 Web 应用程序

本教程是系列教程的第二部分，介绍如何调试本地开发群集上的 Azure Service Fabric 网格应用程序。

本教程的内容：

> [!div class="checklist"]
> * 生成 Azure Service Fabric 网格应用程序时会发生什么情况
> * 如何设置断点来观察服务到服务的调用

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [生成 Service Fabric 网格应用程序](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * 在本地调试应用
> * [将应用发布到 Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 请确保已[设置开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)，包括安装 Service Fabric 运行时、SDK、Docker 和 Visual Studio 2017。

## <a name="download-the-to-do-sample-application"></a>下载待办事项示例应用程序

如果在[本系列教程的第一部分](service-fabric-mesh-tutorial-create-dotnetcore.md)中未创建待办事项示例应用程序，可以下载它。 在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

应用程序处于 `src\todolistapp` 目录下。

## <a name="build-and-debug-on-your-local-cluster"></a>在本地群集上生成和调试

加载项目时，会自动生成一个 Docker 映像并将其部署到本地群集。 此过程可能需要一段时间。 若要在 Visual Studio 的“输出”窗格中监视进度，请将“输出”窗格中的“显示以下来源的输出:”下拉列表值设置为“Service Fabric 工具”。

按 **F5** 在本地编译并运行服务。 在本地运行和调试项目后，Visual Studio 将会：

* 请确保用于 Windows 的 Docker 运行，并设置为使用 Windows 作为容器操作系统。
* 下载任何缺少的 Docker 基础映像。 此过程可能需要一段时间
* 生成（或重新生成）用于托管代码项目的 Docker 映像。
* 在本地 Service Fabric 开发群集上部署并运行容器。
* 运行服务，并命中已设置的任何断点。

完成本地部署并且 Visual Studio 运行应用后，浏览器窗口中会打开默认的示例网页。

**调试提示**

* 如果收到“未运行任何 Service Fabric 本地群集”错误，请确保服务本地群集管理器 (SLCM) 正在运行，然后右键单击任务栏中的 SLCM 图标，并单击“启动本地群集”。 启动本地群集后，返回 Visual Studio 并按 **F5**。
* 如果启动应用时收到 **404** 错误，可能表示 **service.yaml** 中的环境变量不正确。 请确保根据[创建环境变量](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables)中的说明正确设置 `ApiHostPort` 和 `ServiceName`。
* 如果 **service.yaml** 中出现生成错误，请确保使用空格而不是制表符来缩进代码行。 此外，目前必须使用英语区域设置来构建应用。

### <a name="debug-in-visual-studio"></a>在 Visual Studio 中进行调试

在 Visual Studio 中调试 Service Fabric 网格应用程序时，使用的是本地 Service Fabric 开发群集。 若要查看如何从后端服务检索待办事项，请调试到 OnGet() 方法中。
1. 在“WebFrontEnd”项目中，打开“页面” > “Index.cshtml” > “Index.cshtml.cs”，在 **Get** 方法（第 17 行）中设置一个断点。
2. 在“ToDoService”项目中打开“TodoController.cs”，在 **OnGet** 方法（第 15 行）中设置一个断点。
3. 返回浏览器并刷新页面。 命中 Web 前端 `OnGet()` 方法中的断点。 可以检查 `backendUrl` 变量，以查看 **service.yaml** 文件中定义的环境变量如何合并到用于联系后端服务的 URL 中。
4. 逐步执行 (F10) `client.GetAsync(backendUrl).GetAwaiter().GetResult())` 调用，直到命中控制器的 `Get()` 断点。 在此方法中，可以查看如何从内存中列表检索待办事项列表。
5. 完成后，按 **Shift+F5** 停止在 Visual Studio 中调试项目。
 
## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了：

> [!div class="checklist"]
> * 生成 Azure Service Fabric 网格应用程序时会发生什么情况
> * 如何设置断点来观察服务到服务的调用

转到下一教程：
> [!div class="nextstepaction"]
> [发布 Service Fabric 网格 Web 应用程序](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)