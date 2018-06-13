---
title: 开始使用 Azure 表存储和 Visual Studio 连接服务 (ASP.NET) | Microsoft Docs
description: 在使用 Visual Studio 连接服务连接到存储帐户后，如何开始在 Visual Studio 的 ASP.NET 项目中使用 Azure 表存储
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 39456380769e1c3b790d2bbc6fdf9c04c983d054
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798625"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>开始使用 Azure 表存储和 Visual Studio 连接服务 (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概述

Azure 表存储使用户可以存储大量结构化数据。 该服务是一个 NoSQL 数据存储，接受来自 Azure 云内部和外部的通过验证的呼叫。 Azure 表最适合存储结构化非关系型数据。

本教程介绍如何针对 Azure 表存储实体的一些常见使用方案编写 ASP.NET 代码。 这些方案包括创建表，以及添加、查询和删除表实体。 

## <a name="prerequisites"></a>先决条件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>创建 MVC 控制器 

1. 在“解决方案资源管理器”中右键单击“控制器”，并从上下文菜单中选择“添加”->“控制器”。

    ![将控制器添加到 ASP.NET MVC 应用](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. 在“添加基架”对话框中选择“MVC 5 控制器 - 空”，并选择“添加”。

    ![指定 MVC 控制器类型](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. 在“添加控制器”对话框中，将控制器命名为“TablesController”，然后选择“添加”。

    ![命名 MVC 控制器](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. 将以下 *using* 指令添加到 `TablesController.cs` 文件：

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>创建模型类

本文中的许多示例使用名为 **CustomerEntity** 的 **TableEntity** 派生类。 以下步骤逐步指导用户将此类声明为模型类：

1. 在“解决方案资源管理器”中右键单击“模型”，并从上下文菜单中选择“添加”->“类”。

1. 在“添加新项”对话框中，将类命名为 **CustomerEntity**。

1. 打开 `CustomerEntity.cs` 文件并添加以下 **using** 指令：

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. 修改该类，以便在完成后，按以下代码中所示声明该类。 该类声明一个名为 **CustomerEntity** 的实体类，该实体类将客户的名字和姓氏分别用作行键和分区键。

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>创建表

以下步骤演示如何创建表：

> [!NOTE]
> 
> 本部分假设已完成[设置开发环境](#set-up-the-development-environment)中的步骤。 

1. 打开 `TablesController.cs` 文件。

1. 添加名为 **CreateTable** 的方法，用于返回 **ActionResult**。

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. 在 **CreateTable** 方法中，获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用以下代码从 Azure 服务配置中获取存储连接字符串和存储帐户信息：（请将 *&lt;storage-account-name>* 更改为正在访问的 Azure 存储帐户的名称。）
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取表示表服务客户端的 **CloudTableClient** 对象。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 获取表示所需表名称引用的 **CloudTable** 对象。 **CloudTableClient.GetTableReference** 方法不会针对表存储发出请求。 不管该表是否存在，都会返回引用。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 如果表不存在，则调用 **CloudTable.CreateIfNotExists** 方法来创建表。 如果该表不存在但已成功创建，**CloudTable.CreateIfNotExists** 方法将返回 **true**。 否则，将返回 **false**。    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. 将 **ViewBag** 更新为表的名称。

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. 在“解决方案资源管理器”中展开“视图”文件夹，右键单击“表”，并从上下文菜单中选择“添加”->“视图”。

1. 在“添加视图”对话框中，输入 **CreateTable** 作为视图名称，并选择“添加”。

1. 打开 `CreateTable.cshtml`，并按以下代码片段所示对其进行修改：

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. 在“解决方案资源管理器”中，展开“Views”->“Shared”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. 运行应用程序，并选择“创建表”，查看结果是否与以下屏幕截图类似：
  
    ![创建表](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    如前所述，仅当表不存在但已创建时，**CloudTable.CreateIfNotExists** 方法才返回 **true**。 因此，如果在表存在的情况下运行该应用，该方法会返回 **false**。 若要多次运行应用，必须在再次运行应用之前删除表。 可通过 **CloudTable.Delete** 方法删除表。 也可以使用 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)或 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)删除表。  

## <a name="add-an-entity-to-a-table"></a>将实体添加到表

*实体*使用派生自 **TableEntity** 的自定义类映射到 C\# 对象。 要将实体添加到表，请创建用于定义实体的属性的类。 本部分介绍如何定义可将客户的名字和姓氏分别用作行键和分区键的实体类。 实体的分区键和行键共同唯一地标识表中的实体。 查询分区键相同的实体的速度快于查询分区键不同的实体的速度，但使用不同的分区键可实现更高的并行操作可伸缩性。 对于应存储在表服务中的任何属性，该属性必须是公开设置和检索值的受支持类型的公共属性。
实体类*必须*声明公共无参数构造函数。

> [!NOTE]
> 
> 本部分假设已完成[设置开发环境](#set-up-the-development-environment)中的步骤。

1. 打开 `TablesController.cs` 文件。

1. 添加以下指令，使 `TablesController.cs` 文件中的代码可以访问 **CustomerEntity** 类：

    ```csharp
    using StorageAspnet.Models;
    ```

1. 添加名为 **AddEntity** 的方法，用于返回 **ActionResult**。

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. 在 **AddEntity** 方法中，获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用以下代码从 Azure 服务配置中获取存储连接字符串和存储帐户信息：（请将 *&lt;storage-account-name>* 更改为正在访问的 Azure 存储帐户的名称。）
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取表示表服务客户端的 **CloudTableClient** 对象。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 获取 **CloudTable** 对象，该对象表示对新实体要添加到的表的引用。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 实例化并初始化 **CustomerEntity** 类。

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. 创建用于插入客户实体的 **TableOperation** 对象。

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. 通过调用 **CloudTable.Execute** 方法执行插入操作。 可以通过检查 **TableResult.HttpStatusCode** 属性验证操作的结果。 状态代码为 2xx 指示客户端请求的操作已成功处理。 例如，成功插入新的实体会生成 HTTP 状态代码 204，这意味着操作已成功处理，服务器没有返回任何内容。

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. 使用表名称以及插入操作的结果更新 **ViewBag**。

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. 在“解决方案资源管理器”中展开“视图”文件夹，右键单击“表”，并从上下文菜单中选择“添加”->“视图”。

1. 在“添加视图”对话框中，输入 **AddEntity** 作为视图名称，并选择“添加”。

1. 打开 `AddEntity.cshtml`，并按以下代码片段所示对其进行修改：

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. 在“解决方案资源管理器”中，展开“Views”->“Shared”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. 运行应用程序，并选择“添加实体”，查看结果是否与以下屏幕截图类似：
  
    ![添加实体](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    可以遵循[获取单个实体](#get-a-single-entity)部分中的步骤，验证是否已添加实体。 也可以使用 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)查看表的所有实体。

## <a name="add-a-batch-of-entities-to-a-table"></a>向表添加一批实体

除了可以[向表逐个添加实体](#add-an-entity-to-a-table)，还可以批量添加实体。 批量添加实体可以减少代码与 Azure 表服务之间的重复操作次数。 以下步骤演示如何使用单个插入操作向表添加多个实体：

> [!NOTE]
> 
> 本部分假设已完成[设置开发环境](#set-up-the-development-environment)中的步骤。

1. 打开 `TablesController.cs` 文件。

1. 添加名为 **AddEntities** 的方法，用于返回 **ActionResult**。

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. 在 **AddEntities** 方法中，获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用以下代码从 Azure 服务配置中获取存储连接字符串和存储帐户信息：（请将 *&lt;storage-account-name>* 更改为正在访问的 Azure 存储帐户的名称。）
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取表示表服务客户端的 **CloudTableClient** 对象。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 获取 **CloudTable** 对象，该对象表示对新实体要添加到的表的引用。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 基于[将实体添加到表](#add-an-entity-to-a-table)部分中所述的 **CustomerEntity** 模型类实例化一些客户对象。

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. 获取 **TableBatchOperation** 对象。

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. 将实体添加到批量插入操作对象。

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. 通过调用 **CloudTable.ExecuteBatch** 方法执行批量插入操作。   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** 方法返回 **TableResult** 对象的列表，可检查其中的每个 **TableResult** 对象，确定每次操作是成功还是失败。 对于本示例，请将该列表传递给视图，让视图显示每项操作的结果。 
 
    ```csharp
    return View(results);
    ```

1. 在“解决方案资源管理器”中展开“视图”文件夹，右键单击“表”，并从上下文菜单中选择“添加”->“视图”。

1. 在“添加视图”对话框中，输入 **AddEntities** 作为视图名称，并选择“添加”。

1. 打开 `AddEntities.cshtml` 并对其进行修改，使之看起来如下所示。

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. 在“解决方案资源管理器”中，展开“Views”->“Shared”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. 运行应用程序，并选择“添加实体”，查看结果是否与以下屏幕截图类似：
  
    ![添加实体](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    可以遵循[获取单个实体](#get-a-single-entity)部分中的步骤，验证是否已添加实体。 也可以使用 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)查看表的所有实体。

## <a name="get-a-single-entity"></a>获取单个实体

本部分演示如何使用实体的行键和分区键从表中获取单个实体。 

> [!NOTE]
> 
> 本部分假设已完成[设置开发环境](#set-up-the-development-environment)中的步骤，并使用[向表添加一批实体](#add-a-batch-of-entities-to-a-table)中的数据。 

1. 打开 `TablesController.cs` 文件。

1. 添加名为 **GetSingle** 的方法，以便返回 **ActionResult**。

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. 在 **GetSingle** 方法中，获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用以下代码从 Azure 服务配置中获取存储连接字符串和存储帐户信息：（请将 *&lt;storage-account-name>* 更改为正在访问的 Azure 存储帐户的名称。）
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取表示表服务客户端的 **CloudTableClient** 对象。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 获取 **CloudTable** 对象，该对象表示对从中检索实体的表的引用。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 创建检索操作对象，该对象采用派生自 **TableEntity** 的实体对象。 第一个参数是 *partitionKey*，第二个参数是 *rowKey*。 以下代码片段使用[向表添加一批实体](#add-a-batch-of-entities-to-a-table)部分提供的 **CustomerEntity** 类和数据，通过 *partitionKey* 值“Smith”和 *rowKey* 值“Ben”查询表中的 **CustomerEntity** 实体：

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. 执行检索操作。   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. 将结果传递到视图，以显示结果。

    ```csharp
    return View(result);
    ```

1. 在“解决方案资源管理器”中展开“视图”文件夹，右键单击“表”，并从上下文菜单中选择“添加”->“视图”。

1. 在“添加视图”对话框中，输入 **GetSingle** 作为视图名称，并选择“添加”。

1. 打开 `GetSingle.cshtml`，并按以下代码片段所示对其进行修改：

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. 在“解决方案资源管理器”中，展开“Views”->“Shared”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. 运行应用程序，并选择“获取单个实体”，查看结果是否与以下屏幕截图类似：
  
    ![获取单个实体](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>获取分区中的所有实体

如[将实体添加到表](#add-an-entity-to-a-table)部分中所述，分区键和行键的组合唯一标识表中的实体。 查询分区键相同的实体的速度可能快于查询分区键不同的实体的速度。 本部分演示如何通过指定的分区查询表中的所有实体。  

> [!NOTE]
> 
> 本部分假设已完成[设置开发环境](#set-up-the-development-environment)中的步骤，并使用[向表添加一批实体](#add-a-batch-of-entities-to-a-table)中的数据。 

1. 打开 `TablesController.cs` 文件。

1. 添加名为 **GetPartition** 的方法，用于返回 **ActionResult**。

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. 在 **GetPartition** 方法中，获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用以下代码从 Azure 服务配置中获取存储连接字符串和存储帐户信息：（请将 *&lt;storage-account-name>* 更改为正在访问的 Azure 存储帐户的名称。）
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取表示表服务客户端的 **CloudTableClient** 对象。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 获取 **CloudTable** 对象，该对象表示对从中检索实体的表的引用。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 实例化 **TableQuery** 对象，指定 **Where** 子句中的查询。 以下代码片段使用[向表添加一批实体](#add-a-batch-of-entities-to-a-table)部分提供的 **CustomerEntity** 类和数据，通过 **PartitionKey**（客户的姓氏）值“Smith”查询表中的所有实体：

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. 在循环中调用 **CloudTable.ExecuteQuerySegmented** 方法，传递在上一步实例化的查询对象。  **CloudTable.ExecuteQuerySegmented** 方法返回 **TableContinuationToken** 对象，该对象为 **null** 时指示没有更多可检索的实体。 在循环中，使用另一个循环来循环访问返回的实体。 在以下代码示例中，每个返回的实体都已添加到列表中。 循环结束后，该列表将传递到视图供显示： 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. 在“解决方案资源管理器”中展开“视图”文件夹，右键单击“表”，并从上下文菜单中选择“添加”->“视图”。

1. 在“添加视图”对话框中，输入 **GetPartition** 作为视图名称，并选择“添加”。

1. 打开 `GetPartition.cshtml`，并按以下代码片段所示对其进行修改：

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. 在“解决方案资源管理器”中，展开“Views”->“Shared”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. 运行应用程序，并选择“获取分区”，查看结果是否与以下屏幕截图类似：
  
    ![获取分区](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>删除实体

本部分演示如何从表中删除实体。

> [!NOTE]
> 
> 本部分假设已完成[设置开发环境](#set-up-the-development-environment)中的步骤，并使用[向表添加一批实体](#add-a-batch-of-entities-to-a-table)中的数据。 

1. 打开 `TablesController.cs` 文件。

1. 添加名为 **DeleteEntity** 的方法，用于返回 **ActionResult**。

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. 在 **DeleteEntity** 方法中，获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用以下代码从 Azure 服务配置中获取存储连接字符串和存储帐户信息：（请将 *&lt;storage-account-name>* 更改为正在访问的 Azure 存储帐户的名称。）
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取表示表服务客户端的 **CloudTableClient** 对象。
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 获取 **CloudTable** 对象，该对象表示对从中删除实体的表的引用。 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. 创建删除操作对象，该对象采用派生自 **TableEntity** 的实体对象。 本例使用[向表添加一批实体](#add-a-batch-of-entities-to-a-table)部分中所述的 **CustomerEntity** 类和数据。 实体的 **ETag** 必须设置为有效值。  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. 执行删除操作。   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. 将结果传递到视图，以显示结果。

    ```csharp
    return View(result);
    ```

1. 在“解决方案资源管理器”中展开“视图”文件夹，右键单击“表”，并从上下文菜单中选择“添加”->“视图”。

1. 在“添加视图”对话框中，输入 **DeleteEntity** 作为视图名称，并选择“添加”。

1. 打开 `DeleteEntity.cshtml`，并按以下代码片段所示对其进行修改：

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. 在“解决方案资源管理器”中，展开“Views”->“Shared”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. 运行应用程序，并选择“删除实体”，查看结果是否与以下屏幕截图类似：
  
    ![获取单个实体](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>后续步骤
查看更多功能指南，以了解在 Azure 中存储数据的其他方式。

  * [Azure Blob 存储和 Visual Studio 连接服务入门 (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [开始使用 Azure 队列存储和 Visual Studio 连接服务 (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
