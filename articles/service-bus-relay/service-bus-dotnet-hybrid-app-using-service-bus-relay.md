---
title: "Azure WCF 中继本地/云混合应用程序 (.NET) | Microsoft Docs"
description: "了解如何使用 Azure WCF 中继创建 .NET 本地/云混合应用程序。"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/16/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: 6c59c98a400da0616762b2bd0c4217d97e22ab86
ms.lasthandoff: 03/01/2017


---
# <a name="net-on-premisescloud-hybrid-application-using-azure-wcf-relay"></a>使用 Azure WCF 中继创建 .NET 本地/云混合应用程序
## <a name="introduction"></a>介绍
本文演示如何使用 Microsoft Azure 和 Visual Studio 生成混合云应用程序。 本教程假定你之前未使用过 Azure。 在不到 30 分钟的时间内，你就能让使用多个 Azure 资源的应用程序在云中启动并运行。

你将学习以下内容：

* 如何创建或修改现有 Web 服务以供 Web 解决方案使用。
* 如何使用 Azure WCF 中继服务在 Azure 应用程序和托管于其他某处的 Web 服务之间共享数据。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Azure 中继功能将为混合解决方案带来哪些帮助
业务解决方案通常由为处理独特的新业务需求而编写的自定义代码和已有的解决方案和系统所提供的现有功能组成。

解决方案架构师开始使用云来轻松地处理缩放需求和降低运营成本。 在此过程中，他们发现希望用作其解决方案的构建基块的现有服务资产位于企业防火墙内，无法通过云解决方案轻松访问。 许多内部服务的构建或托管方式使得它们无法在企业网络边缘轻松公开。

[Azure 中继](https://azure.microsoft.com/services/service-bus/)的设计考虑到如何利用现有的 Windows Communication Foundation (WCF) Web 服务，使得位于企业外部的解决方案能够安全地访问这些服务，而无需对企业网络基础结构进行彻底的更改。 虽然此类中继服务仍托管在现有环境中，但它们会将侦听传入会话和请求这一任务委托给云托管的中继服务。 Azure 中继还会通过使用[共享访问签名 (SAS)](../service-bus-messaging/service-bus-sas.md) 身份验证来保护这些服务，以阻止未经授权的访问。

## <a name="solution-scenario"></a>解决方案应用场景
在本教程中，你将创建一个 ASP.NET 网站，用于查看产品库存页上的产品列表。

![][0]

本教程假定你的产品信息位于现有的本地系统中，而且你使用 Azure 中继来访问该系统。 这是由在简单的控制台应用程序中运行的 Web 服务模拟的，并由一系列内存中产品提供支持。 你将能够在你自己的计算机上运行此控制台应用程序并将 Web 角色部署到 Azure 中。 通过此操作，你将看到在 Azure 数据中心运行的 Web 角色确实会调入你的计算机，即使你的计算机几乎肯定会驻留在至少一个防火墙和一个网络地址转换 (NAT) 层后面，情况也是如此。

下面是已完成的 Web 应用程序的起始页的屏幕截图。

![][1]

## <a name="set-up-the-development-environment"></a>设置开发环境
在开始开发 Azure 应用程序之前，需要下载工具并设置开发环境：

1. 从 SDK [下载页](https://azure.microsoft.com/downloads/)安装用于 .NET 的 Azure SDK。
2. 在“.NET”列中，单击要使用的 [Visual Studio](http://www.visualstudio.com) 版本。 本教程中的步骤使用 Visual Studio 2015。
3. 当提示你是要运行还是保存安装程序时，单击“运行” 。
4. 在“Web 平台安装程序”中，单击“安装”，然后继续安装。
5. 安装完成后，你就有了开始开发应用所需的一切。 SDK 包含了一些工具，可利用这些工具在 Visual Studio 中轻松开发 Azure 应用程序。

## <a name="create-a-namespace"></a>创建命名空间
若要开始在 Azure 中使用中继功能，必须先创建一个服务命名空间。 命名空间提供了用于对应用程序中的 Azure 资源进行寻址的范围容器。 请按照[此处的说明](relay-create-namespace-portal.md)创建中继命名空间。

## <a name="create-an-on-premises-server"></a>创建本地服务器
首先，你将构建 (mock) 本地产品目录系统。 这将非常简单；可以认为，此系统代表一个实际存在的本地产品目录系统，其中包含我们将尝试集成的完整服务图面。

此项目是一个 Visual Studio 控制台应用程序，它使用 [Azure 服务总线 NuGet 包](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 来包含服务总线库和配置设置。

### <a name="create-the-project"></a>创建项目
1. 使用管理员特权启动 Microsoft Visual Studio。 为此，请右键单击 Visual Studio 程序图标，然后单击“以管理员身份运行”。
2. 在 Visual Studio 的“文件”菜单中，单击“新建”，然后单击“项目”。
3. 从“已安装的模板”的“Visual C#”下单击“控制台应用程序”。 在“名称”框中，键入名称“ProductsServer”：

   ![][11]
4. 单击“确定”以创建“ProductsServer”项目。
5. 如果你已为 Visual Studio 安装 NuGet 包管理器，请跳到下一步骤。 否则，请访问 [NuGet][NuGet]，然后单击“安装 NuGet”。[](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 按照提示操作以安装 NuGet 包管理器，然后重新启动 Visual Studio。
6. 在解决方案资源管理器中，右键单击“ProductsServer”项目，然后单击“管理 NuGet 程序包”。
7. 单击“浏览”选项卡，然后搜索 `Microsoft Azure Service Bus`。 单击“安装” 并接受使用条款。

   ![][13]

   请注意，现已引用所需的客户端程序集。
8. 为产品协定添加新类。 在“解决方案资源管理器”中，右键单击“ProductsServer”项目，单击“添加”，然后单击“类”。
9. 在“名称”框中，键入名称 **ProductsContract.cs**。 。
10. 在“ProductsContract.cs”中，将命名空间定义替换为以下代码，以定义服务的协定。

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```
11. 在 Program.cs 中，将命名空间定义替换为以下代码，以为其添加配置文件服务和主机。

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```
12. 在“解决方案资源管理器”中，双击“App.config”文件以在 Visual Studio 编辑器中将其打开。 在 `<system.ServiceModel>` 元素的下面（仍在 `<system.ServiceModel>` 中）添加以下 XML 代码。 确保将 yourServiceNamespace 替换为命名空间的名称，并将 yourKey 替换为之前从门户中检索到的 SAS 密钥：

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
13. 仍在 App.config 文件中，在 `<appSettings>` 元素中，将连接字符串值替换为之前从门户获取的连接字符串。

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. 按 **Ctrl+Shift+B** 或从“生成”菜单中单击“生成解决方案”生成应用程序，并验证到目前为止操作的准确性。

## <a name="create-an-aspnet-application"></a>创建 ASP.NET 应用程序
在本部分中，你将生成一个简单的 ASP.NET 应用程序，以便显示你的产品服务中检索到的数据。

### <a name="create-the-project"></a>创建项目
1. 确保使用管理员权限运行 Visual Studio。
2. 在 Visual Studio 的“文件”菜单中，单击“新建”，然后单击“项目”。
3. 从“已安装的模板”的“Visual C#”下单击“ASP.NET Web 应用程序”。 **ProductsPortal**。 。

   ![][15]
4. 从“选择模板”列表中，单击“MVC”。
5. 选中“ **在云中托管**”框。

   ![][16]
6. 单击“ **更改身份验证** ”按钮。 在“更改身份验证”对话框中，单击“无身份验证”，然后单击“确定”。 在本教程中，你将部署无需用户登录名的应用。

    ![][18]
7. 在“新建 ASP.NET 项目”对话框的“Microsoft Azure”部分中，确保已选择“在云中托管”并且在下拉列表中已选择“应用服务”。

   ![][19]
8. 单击 **“确定”**。
9. 现在必须配置新 Web 应用的 Azure 资源。 执行[创建 Web 应用程序](../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application)和[创建 Azure 资源](../app-service-web/web-sites-dotnet-get-started.md#create-the-azure-resources)中的所有步骤。 然后，返回到本教程并继续执行下一步。
10. 在解决方案资源管理器中，右键单击“模型”，然后依次单击“添加”和“类”。 在“名称”框中，键入名称“Product.cs”。 。

    ![][17]

### <a name="modify-the-web-application"></a>修改 Web 应用程序
1. 在 Visual Studio 的 Product.cs 文件中将现有命名空间定义替换为以下代码。

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```
2. 在解决方案资源管理器中，展开 **Controllers** 文件夹，然后双击 **HomeController.cs** 文件以在 Visual Studio 中将其打开。
3. 在 **HomeController.cs**中，将现有命名空间定义替换为以下代码。

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```
4. 在解决方案资源管理器中，展开 Views\Shared 文件夹，然后双击 **_Layout.cshtml**以在 Visual Studio 编辑器中将其打开。
5. 将每一处 **My ASP.NET Application** 更改为 **LITWARE's Products**。
6. 删除“Home”、“About”和“Contact”链接。 在下面的示例中，删除突出显示的代码。

    ![][41]
7. 在解决方案资源管理器中，展开 Views\Home 文件夹，然后双击 **Index.cshtml**以在 Visual Studio 编辑器中将其打开。
   将文件的全部内容替换为以下代码。

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```
8. 若要验证到目前为止操作的准确性，可以按 **Ctrl+Shift+B** 生成项目。

### <a name="run-the-app-locally"></a>在本地运行应用
运行应用程序以验证其是否正常运行。

1. 确保 **ProductsPortal**是活动项目。 在“解决方案资源管理器”中，右键单击项目名称并选择“设置为启动项目”。
2. 在 Visual Studio 中，按 F5。
3. 应用程序应在浏览器中显示为正在运行。

   ![][21]

## <a name="put-the-pieces-together"></a>将各个部分组合在一起
下一步是将本地产品服务器与 ASP.NET 应用程序挂钩。

1. 如果尚未打开在“ **创建 ASP.NET 应用程序** ”一节中创建的 [ProductsPortal](#create-an-aspnet-application) 项目，请在 Visual Studio 中重新打开该项目。
2. 采用与“创建本地服务器”部分类似的步骤，将 NuGet 包添加到项目“引用”中。 在解决方案资源管理器中，右键单击“ProductsPortal”项目，然后单击“管理 NuGet 程序包”。
3. 搜索“服务总线”并选择“Microsoft Azure 服务总线”项。 然后，完成安装过程并关闭此对话框。
4. 在解决方案资源管理器中，右键单击“ProductsPortal”项目，然后单击“添加”，再单击“现有项”。
5. 从 **ProductsServer** 控制台项目导航到 **ProductsContract.cs** 文件。 单击以突出显示 ProductsContract.cs。 单击“添加”旁边的向下箭头，然后单击“添加为链接”。

   ![][24]
6. 现在，在 Visual Studio 编辑器中打开 **HomeController.cs** 文件，并将命名空间定义替换为以下代码。 确保将 yourServiceNamespace 替换为你的服务命名空间的名称，并将 yourKey 替换为你的 SAS 密钥。 这将使客户端能够调用本地服务，并返回调用的结果。

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```
7. 在解决方案资源管理器中，右键单击 **productsportal** 解决方案（确保右键单击解决方案，而不是项目）。 单击“添加”，然后单击“现有项目”。
8. 导航到 **ProductsServer** 项目，然后双击“ProductsServer.csproj”解决方案文件以将其添加。
9. **ProductsServer** 必须正在运行，才能在 **ProductsPortal** 上显示数据。 在解决方案资源管理器中，右键单击“ProductsPortal”解决方案并单击“属性”。 **属性页** ”对话框。
10. 在左侧，单击“启动项目”。 在右侧，单击“多个启动项目”。 确保 **ProductsServer** 和 **ProductsPortal** 按此顺序显示，并且将“启动”设置为两者的操作。

      ![][25]
11. 仍在“属性”对话框中，单击左侧的“项目依赖项”。
12. 在“项目”列表中，单击“ProductsServer”。 确保**未**选择“ProductsPortal”。
13. 在“项目”列表中，单击“ProductsPortal”。 确保已选择 **ProductsServer** 。

    ![][26]
14. 单击你正在使用的 Visual Studio 版本的“安装 SDK”  in the **属性页** ”。

## <a name="run-the-project-locally"></a>在本地运行项目
若要在本地测试应用程序，请在 Visual Studio 中按 **F5**。 本地服务器 (**ProductsServer**) 应该会先启动，然后 **ProductsPortal** 应用程序应该会在浏览器窗口中启动。 这次，你将看到产品库存列出了从产品服务本地系统中检索到的数据。

![][10]

按“ProductsPortal”页上的“刷新”。 每次刷新该页面时，都会看到服务器应用在调用来自 **ProductsServer** 的 `GetProducts()` 时显示一条消息。

关闭这两个应用程序，然后再继续执行下一步。

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>将 ProductsPortal 项目部署到 Azure Web 应用
下一步是将 **ProductsPortal** 前端转换为 Azure Web 应用。 首先，部署 **ProductsPortal** 项目，按照[将 Web 项目部署到 Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-azure) 一节中的所有步骤操作。 部署完成后，返回到本教程并继续执行下一步。

> [!NOTE]
> 在部署后自动启动 **ProductsPortal** Web 项目时，你可能会在浏览器窗口中看到错误消息。 这在意料之中，因为 **ProductsServer** 应用程序尚未运行。
>
>

复制已部署 Web 应用的 URL，因为你在下一个步骤中需要用到该 URL。 你也可以从 Visual Studio 的“Azure App Service 活动”窗口中获取此 URL：

![][9]

### <a name="set-productsportal-as-web-app"></a>将 ProductsPortal 设置为 Web 应用
在云中运行应用程序之前，必须确保 **ProductsPortal** 从 Visual Studio 内以 Web 应用的形式启动。

1. 在 Visual Studio 中，右键单击“ProjectsPortal”项目，然后单击“属性”。
2. 在左侧列中，单击 **Web**。
3. 在“启动操作”部分中，单击“启动 URL”按钮，然后在文本框中输入你先前部署的 Web 应用的 URL；例如 `http://productsportal1234567890.azurewebsites.net/`。

    ![][27]
4. 从 Visual Studio 的“文件”菜单中，单击“全部保存”。
5. 从 Visual Studio 的“生成”菜单中，单击“ **重新生成解决方案**”。

## <a name="run-the-application"></a>运行应用程序
1. 按 F5 生成并运行应用程序。 本地服务器（**ProductsServer** 控制台应用程序）应该会先启动，然后 **ProductsPortal** 应用程序应该会在浏览器窗口中启动，如以下屏幕截图所示。 再次提请注意，产品库存列表会列出从产品服务本地系统检索到的数据，并在 Web 应用中显示该数据。 请检查 URL，确保 **ProductsPortal** 正在云中以 Azure Web 应用的形式运行。

   ![][1]

   > [!IMPORTANT]
   > **ProductsServer** 控制台应用程序必须正在运行，而且能够为 **ProductsPortal** 应用程序提供数据。 如果浏览器显示错误，请再多等几秒钟，让 **ProductsServer** 加载并显示以下消息。 然后按浏览器中的“ **刷新** ”。
   >
   >

   ![][37]
2. 返回到浏览器中，按“ProductsPortal”页上的“刷新”。 每次刷新该页面时，都会看到服务器应用在调用来自 **ProductsServer** 的 `GetProducts()` 时显示一条消息。

    ![][38]

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 中继的详细信息，请参阅以下资源：  

* [什么是 Azure 中继？](relay-what-is-it.md)  
* [如何使用中继](service-bus-dotnet-how-to-use-relay.md)  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png

