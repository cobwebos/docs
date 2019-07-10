---
title: 通过 Azure Cosmos DB 模拟器生成任务设置 CI/CD 管道
description: 教程：如何使用 Cosmos DB 模拟器生成任务在 Azure DevOps 中设置生成和发布工作流
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 19ced9767d77b0d7bfcec6f01425ab1089a55d54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069232"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>在 Azure DevOps 中通过 Azure Cosmos DB 模拟器生成任务设置 CI/CD 管道

为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 可以利用模拟器在本地开发和测试应用程序，无需创建 Azure 订阅且不会产生任何费用。 

使用针对 Azure DevOps 的 Azure Cosmos DB 模拟器生成任务，可以在 CI 环境中执行相同的操作。 使用生成任务时，可以在生成和发布工作流中针对模拟器运行测试。 该任务启动一个包含已运行模拟器的 Docker 容器，并提供一个可供生成定义的其余部分使用的终结点。 可以根据需要创建并启动模拟器的多个实例，每一个都在单独的容器中运行。 

本文演示如何在适用于 ASP.NET 应用程序的 Azure DevOps 中设置 CI 管道，该应用程序使用 Cosmos DB 模拟器生成任务来运行测试。 可以使用类似的方法为 Node.js 或 Python 应用程序设置 CI 管道。 

## <a name="install-the-emulator-build-task"></a>安装模拟器生成任务

若要使用生成任务，需先将它安装到 Azure DevOps 组织。  在[市场](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview)中找到“Azure Cosmos DB 模拟器”，然后单击“免费获取”。 

![在 Azure DevOps 市场中找到并安装 Azure Cosmos DB 模拟器生成任务](./media/tutorial-setup-ci-cd/addExtension_1.png)

接下来，选择要在其中安装扩展的组织。 

> [!NOTE]
> 要将扩展安装到 Azure DevOps 组织，必须是帐户所有者或项目集合管理员。 如果你没有权限，但却是帐户成员，可以改为请求扩展。 [了解详细信息。](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions)

![选择要在其中安装扩展的 Azure DevOps 组织](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>创建生成定义

安装扩展以后，请登录 Azure DevOps 帐户，从项目仪表板中找到项目。 可以向项目添加[生成管道](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)，也可以修改现有的生成管道。 如果已经有生成管道，则可跳转到[向生成定义添加模拟器生成任务](#addEmulatorBuildTaskToBuildDefinition)。

1. 若要创建新的生成定义，请导航到 Azure DevOps 中的 **“生成”** 选项卡。 选择 **“+新建”** 。 \> **新建生成管道**

   ![创建新的生成管道](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. 选择所需**源**、**团队项目**、**存储库**和**手动和计划生成的默认分库**。 选择所需选项后，请选择“继续”  。

   ![针对生成管道选择团队项目、存储库和分库](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. 最后，选择生成管道所需的模板。 在本教程中，我们将选择 **ASP.NET** 模板。 

> [!NOTE]
> 为此 CI 选择的代理池应该安装用于 Windows 的 Docker，除非已在以前的任务中作为 CI 的一部分手动完成该安装。 有关代理池的选择，请参阅 [Microsoft 托管代理](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml)一文；我们建议从 `Hosted VS2017` 或 `Hosted VS2019` 开始。 

现在，我们有了一个生成管道，可以通过设置它来使用 Azure Cosmos DB 模拟器生成任务。 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>将任务添加到生成管道

1. 在向生成管道添加任务之前，应先添加代理作业。 导航到生成管道，选择“...”，然后选择“添加代理作业”。  

1. 接下来选择代理作业旁边的 **+** 符号，以便添加模拟器生成任务。 在搜索框中搜索 **cosmos**，选择“Azure Cosmos DB 模拟器”，然后将其添加到代理作业。  此生成任务会启动一个容器，其中的 Cosmos DB 模拟器实例已经运行。 应该将 Azure Cosmos DB 模拟器任务置于任何其他预期模拟器会处于运行状态的任务之前。

   ![向生成定义添加模拟器生成任务](./media/tutorial-setup-ci-cd/addExtension_3.png)

在本教程中，我们会将任务添加到开始的时候，确保模拟器在测试执行之前可用。

## <a name="configure-tests-to-use-the-emulator"></a>配置测试，以便使用模拟器

现在需配置测试，以便使用模拟器。 模拟器生成任务导出环境变量“CosmosDbEmulator.Endpoint”，生成管道中的任何其他任务都可以针对其发出请求。 

在本教程中，我们将使用 [Visual Studio 测试任务](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md)运行通过 **.runsettings** 文件配置的单元测试。 若要详细了解单元测试设置，请访问此[文档](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017)。 本文档中使用的完整 Todo 应用程序代码示例可在 [Github](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) 上找到

下面是一个 **.runsettings** 文件的示例，该文件定义需传递到应用程序的单元测试中的参数。 请注意，所使用的 `authKey` 变量是模拟器的[已知密钥](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests)。 此 `authKey` 是模拟器生成任务预期使用的密钥，应该在 **.runsettings** 文件中定义。

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

如果正在为使用 Azure Cosmos DB 的 MongoDB API 的应用程序设置 CI/CD 管道，则连接字符串默认包含端口号 10255。 但是，此端口当前未打开，作为替代方法，你应该使用端口 10250 建立连接。 Azure Cosmos DB 用于 MongoDB 连接字符串的 API 保持不变，不同的是支持的端口号是 10250 而不是 10255。

这些参数 (`TestRunParameters`) 通过应用程序的测试项目中的 `TestContext` 属性引用。 下面是一个针对 Cosmos DB 运行的测试的示例。

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

导航到 Visual Studio 测试任务中的“执行选项”。 在“设置文件”选项中，指定测试使用  **.runsettings** 文件进行配置。 在“替代测试运行参数”选项中，添加 `-endpoint $(CosmosDbEmulator.Endpoint)`。  这样做会将测试任务配置为引用模拟器生成任务的终结点，而不是在 **.runsettings** 文件中定义的终结点。  

![使用模拟器生成任务终结点替代终结点变量](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>运行生成

现在，请对该生成执行“保存并排队”操作。  

![保存并运行生成](./media/tutorial-setup-ci-cd/runBuild_1.png)

生成启动以后，就会观察到 Cosmos DB 模拟器任务开始使用已安装的模拟器拉取 Docker 映像。 

![保存并运行生成](./media/tutorial-setup-ci-cd/runBuild_4.png)

生成完成以后，就会观察到测试通过，所有这些测试都是针对生成任务中的 Cosmos DB 模拟器运行的！

![保存并运行生成](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>后续步骤

若要详细了解如何将模拟器用于本地开发和测试，请参阅[将 Azure Cosmos DB 模拟器用于本地开发和测试](https://docs.microsoft.com/azure/cosmos-db/local-emulator)。

若要导出模拟器 SSL 证书，请参阅[导出 Azure Cosmos DB 模拟器证书供 Java、Python 和 Node.js 使用](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
