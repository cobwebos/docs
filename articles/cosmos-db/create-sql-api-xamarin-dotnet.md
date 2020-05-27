---
title: Azure Cosmos DB：使用 Xamarin 生成 todo 应用
description: 演示了一个可以用来连接到 Azure Cosmos DB 并进行查询的 Xamarin 代码示例
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 3278374a0a09ed4e776ba0f773ad2dafd9786164
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652008"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>快速入门：通过 Azure Cosmos DB SQL API 帐户使用 Xamarin 生成 ToDo 应用

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。

> [!NOTE]
> 在 GitHub 上的[此文档](https://github.com/xamarinhq/app-geocontacts)中，可以找到整个规范示例 Xamarin 应用的示例代码，其中展示了多个 Azure 产品，包括 CosmosDB。 此应用演示如何查看地理分散的联系人，并让这些联系人更新其位置。

本快速入门演示如何使用 Azure 门户创建 Azure Cosmos DB SQL API 帐户、文档数据库和容器。 然后，你将使用 [Xamarin.Forms](https://docs.microsoft.com/xamarin/) 和 [MVVM 体系结构模式](https://docs.microsoft.com/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm)生成并部署一个基于 [SQL .NET API](sql-api-sdk-dotnet.md) 和 [Xamarin](https://docs.microsoft.com/xamarin/) 的待办事项列表移动应用。

![在 iOS 上运行的 Xamarin ToDo 应用](./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png)

## <a name="prerequisites"></a>先决条件

如果是在 Windows 上开发，且尚未安装 Visual Studio 2019，可以下载并使用**免费的** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”和“使用 .NET 进行移动开发”工作负荷。 

如果使用的是 Mac，则可以下载**免费的** [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>添加容器

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>添加示例数据

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查询数据

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在请从 GitHub 克隆 Xamarin SQL API 应用、查看代码、获取 API 密钥，然后运行该应用。 会看到以编程方式处理数据是多么容易。

1. 打开命令提示符，新建一个名为“git-samples”的文件夹，然后关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. 然后，在 Visual Studio 中打开 samples/xamarin/ToDoItems 文件夹中的 ToDoItems.sln 文件。

## <a name="obtain-your-api-keys"></a>获取 API 密钥

返回到 Azure 门户，获取 API 密钥信息并将其复制到应用中。

1. 在 [Azure 门户](https://portal.azure.com/)的 Azure Cosmos DB SQL API 帐户的左侧导航栏中，单击“密钥”，然后单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 APIKeys.cs 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-sql-api-xamarin-dotnet/keys.png)

2. 在 Visual Studio 2019 或 Visual Studio for Mac 中，打开 azure-cosmos-db-sql-xamarin-getting-started/src/ToDoItems.Core/Helpers 文件夹中的 APIKeys.cs 文件。

3. 从门户中复制 URI 值（使用复制按钮），并在 APIKeys.cs 中将其设为 `CosmosEndpointUrl` 变量的值。

    `public static readonly string CosmosEndpointUrl = "";`

4. 然后从门户复制“主密钥”的值，并在 APIKeys.cs 中将其设为 `Cosmos Auth Key` 的值。

    `public static readonly string CosmosAuthKey = "";`

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>查看代码

此解决方案演示如何使用 Azure Cosmos DB SQL API 和 Xamarin.Forms 创建 ToDo 应用。 此应用有两个选项卡，第一个选项卡包含一个列表视图，显示尚未完成的代办事项。 第二选项卡显示已完成的代办事项。 除了在第一个选项卡中查看未完成的待办事项，还可以添加新的待办事项、编辑现有的待办事项，以及将待办事项标记为已完成。

![通过复制添加 json 数据，然后在 Azure 门户的数据资源管理器中单击“保存”](./media/create-sql-api-xamarin-dotnet/android-todo-screen.png)

ToDoItems 解决方案中的代码包含：

* ToDoItems.Core：这是一个 .NET Standard 项目，其中包含一个 Xamarin.Forms 项目以及用于在 Azure Cosmos DB 中保留待办事项的共享应用程序逻辑代码。
* ToDoItems.Android：此项目包含 Android 应用。
* ToDoItems.iOS：此项目包含 iOS 应用。

现在，请快速查看应用如何与 Azure Cosmos DB 通信。

* 需将 [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) NuGet 包添加到所有项目。
* azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Models 文件夹中的 `ToDoItem` 类为上面创建的 **Items** 容器中的文档建模。 请注意，属性命名区分大小写。
* azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Services 文件夹中的 `CosmosDBService` 类将通信封装到 Azure Cosmos DB。
* `CosmosDBService` 类中有一个 `DocumentClient` 类型的变量。 `DocumentClient` 用于针对 Azure Cosmos DB 帐户配置和执行请求，并进行实例化：

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* 查询容器中的文档时，使用 `DocumentClient.CreateDocumentQuery<T>` 方法，如下面的 `CosmosDBService.GetToDoItems` 函数所示：

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    `CreateDocumentQuery<T>` 采用的 URI 指向在上一部分创建的容器。 还可以指定 LINQ 运算符，例如 `Where` 子句。 在这种情况下，仅返回尚未完成的待办事项。

    `CreateDocumentQuery<T>` 函数是同步执行的，返回 `IQueryable<T>`。 不过，`AsDocumentQuery` 方法可以将 `IQueryable<T>` 转换为 `IDocumentQuery<T>` 对象，后者可以异步执行。 因此，不会阻止移动应用程序的 UI 线程。

    `IDocumentQuery<T>.ExecuteNextAsync<T>` 函数从 Azure Cosmos DB 检索结果页，该 DB 会进行 `HasMoreResults` 检查，看是否还有其他需要返回的结果。

> [!TIP]
> 多个在 Azure Cosmos 容器和文档上运行的函数采用 URI 作为参数，以便指定容器或文档的地址。 此 URI 使用 `URIFactory` 类进行构造。 数据库、容器和文档的 URI 均可通过此类来创建。

* `ComsmosDBService.InsertToDoItem` 函数演示如何插入新文档：

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    指定了项 URI 以及要插入的项。

* `CosmosDBService.UpdateToDoItem` 函数演示如何将现有文档替换为新文档：

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    此处需要使用新的 URI 来唯一标识要替换的文档，而获得该 URI 的方法是先使用 `UriFactory.CreateDocumentUri`，然后向其传递数据库和容器的名称以及文档的 ID。

    `DocumentClient.ReplaceDocumentAsync` 将通过 URI 标识的文档替换为已指定为参数的文档。

* `CosmosDBService.DeleteToDoItem` 函数演示了如何删除某个项：

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    再次请注意这个在创建后传递给 `DocumentClient.DeleteDocumentAsync` 函数的唯一的文档 URI。

## <a name="run-the-app"></a>运行应用

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。

以下步骤将演示如何使用 Visual Studio for Mac 调试器来运行应用。

> [!NOTE]
> Android 版应用的使用完全相同。如果有差异，则会在下面的步骤中指出。 如果希望在 Windows 上使用 Visual Studio 进行调试，可参阅[此处 (iOS)](https://docs.microsoft.com/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) 和[此处 (Android)](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/) 的相关操作文档。

1. 首先选择要作为目标的平台，方法是：单击突出显示的下拉列表，然后选择适用于 iOS 的 ToDoItems.iOS 或适用于 Android 的 ToDoItems.Android。

    ![选择可在 Visual Studio for Mac 中进行调试的平台](./media/create-sql-api-xamarin-dotnet/ide-select-platform.png)

2. 若要开始调试此应用，请按 cmd+Enter 或单击“开始”按钮。

    ![开始在 Visual Studio for Mac 中调试](./media/create-sql-api-xamarin-dotnet/ide-start-debug.png)

3. 当 iOS 模拟器或 Android Emulator 启动完毕，此应用会在屏幕底部 (iOS) 或顶部 (Android) 显示 2 个选项卡。 第一个选项卡显示未完成的待办事项，第二个选项卡显示已完成的代办事项。

    ![ToDo 应用的启动屏幕](./media/create-sql-api-xamarin-dotnet/ios-droid-started.png)

4. 若要在 iOS 上完成某个待办事项，请向左滑动，然后点击“完成”按钮。 若要在 Android 上完成某个待办事项，请长按该项，然后点击“完成”按钮。

    ![完成待办事项](./media/create-sql-api-xamarin-dotnet/simulator-complete.png)

5. 若要编辑某个待办事项，请点击该项，此时会出现一个新的屏幕，供你输入新值。 点击“保存”按钮会将所做的更改保存到 Azure Cosmos DB。

    ![编辑待办事项](./media/create-sql-api-xamarin-dotnet/simulator-edit.png)

6. 若要添加待办事项，请点击主屏幕右上角的“添加”按钮，然后就会出现一个新的空白编辑页。

    ![添加待办事项](./media/create-sql-api-xamarin-dotnet/simulator-add.png)

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门教程已介绍如何创建 Azure Cosmos 帐户、使用数据资源管理器创建容器，以及生成和部署控制台应用。 现在可将其他数据导入 Azure Cosmos 帐户。

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
