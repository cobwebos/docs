---
title: "Azure 服务总线 WCF 中继教程 | Microsoft Docs"
description: "使用 WCF 中继构建服务总线客户端应用程序和服务。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: sethm
ms.openlocfilehash: 0298a93da0d8cd0b1f2e15146a708c8dd6ecb8e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-wcf-relay-tutorial"></a>Azure WCF 中继教程

本教程介绍如何使用 Azure 中继功能构建简单的 WCF 中继客户端应用程序和服务。 有关使用[服务总线消息传送](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging)的类似教程，请参阅[服务总线队列入门](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。

通过此教程，可以了解创建 WCF 中继客户端和服务应用程序所需的步骤。 正如原始的 WCF，服务是公开一个或多个终结点的构造，其中每个终结点都公开一个或多个服务操作。 服务的终结点用于指定可在其中找到服务的地址、包含客户端必须与服务进行通信的信息的绑定，以及定义服务向其客户端提供的功能的协定。 WCF 和 WCF 中继之间的主要区别在于：终结点在云中公开，而不是在本地计算机中公开。

完成本教程中的一系列主题后，将具有一项正在运行的服务和可以调用服务操作的客户端。 第一个主题描述了如何设置帐户。 接下来的步骤描述了如何定义使用协定的服务、如何实现服务，以及如何使用代码配置该服务。 这些主题还描述了如何托管和运行该服务。 创建的服务是自托管的，并且客户端和服务在同一台计算机上运行。 可以通过使用代码或配置文件配置服务。

最后三个步骤介绍如何创建客户端应用程序、如何配置客户端应用程序，以及如何创建和使用可以访问主机功能的客户端。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要以下各项：

* [Microsoft Visual Studio 2015 或更高版本](http://visualstudio.com)。 本教程使用 Visual Studio 2017。
* 有效的 Azure 帐户。 如果没有帐户，只需几分钟的时间就能创建一个免费帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/free/)。

## <a name="create-a-service-namespace"></a>创建服务命名空间

第一步是创建命名空间并获取[共享访问签名 (SAS)](../service-bus-messaging/service-bus-sas.md) 密钥。 命名空间为每个通过中继服务公开的应用程序提供应用程序边界。 创建服务命名空间时，系统会自动生成 SAS 密钥。 服务命名空间与 SAS 密钥的组合为 Azure 提供了一个用于验证应用程序访问权限的凭据。 请按照[此处的说明](relay-create-namespace-portal.md)创建中继命名空间。

## <a name="define-a-wcf-service-contract"></a>定义 WCF 服务协定

此服务协定指定服务支持的操作（方法或函数的 Web 服务术语）。 约定通过定义 C++、C# 或 Visual Basic 接口来创建。 接口中的每个方法都对应一个特定的服务操作。 必须将 [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 属性应用于每个接口，并且必须将 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 属性应用于每个操作。 如果具有 [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 属性的接口中的方法没有 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 属性，则该方法是不公开的。 该过程后面的示例中提供了这些任务的代码。 有关协定和服务的更多讨论，请参阅 WCF 文档中的 [设计和实现服务](https://msdn.microsoft.com/library/ms729746.aspx) 。

### <a name="create-a-relay-contract-with-an-interface"></a>使用接口创建中继协定

1. 在“开始”菜单中右键单击 Visual Studio，以便以管理员身份启动该程序，并选择“以管理员身份运行”。
2. 创建新的控制台应用程序项目。 单击“文件”菜单并选择“新建”，并单击“项目”。 在“新建项目”对话框中，单击“Visual C#”（如果“Visual C#”未出现，则在“其他语言”下方查看）。 单击“控制台应用 (.NET Framework)”模板，并将其命名为“EchoService”。 单击“确定”以创建该项目  。

    ![][2]

3. 安装服务总线 NuGet 包。 该包自动添加对服务总线库和 WCF **System.ServiceModel**的引用。 [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) 是可以以编程方式访问 WCF 基本功能的命名空间。 服务总线使用 WCF 的许多对象和属性来定义服务约定。

    在“解决方案资源管理器”中，右键单击项目，然后单击“管理 NuGet 程序包”。单击“浏览”选项卡，并搜索 `Microsoft Azure Service Bus`。 确保在“版本”  框中选定项目名称。 单击“安装” 并接受使用条款。

    ![][3]
4. 在解决方案资源管理器中，双击 Program.cs 文件以在编辑器中将其打开（如果尚未打开）。
5. 在文件的顶部添加以下 using 语句：

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. 将命名空间的默认名称 **EchoService** 更改为 **Microsoft.ServiceBus.Samples**。

   > [!IMPORTANT]
   > 本教程使用 C# 命名空间“Microsoft.ServiceBus.Samples”，它是基于协定的管理类型的命名空间，此类型用于[配置 WCF 客户端](#configure-the-wcf-client)步骤中的配置文件。 在构建此示例时，可以指定任何想要的命名空间，在配置文件中修改了协定以及相应服务的命名空间后，本教程才会生效。 在 App.config 文件中指定的命名空间必须与在 C# 文件中指定的命名空间相同。
   >
   >
7. 直接在 `Microsoft.ServiceBus.Samples` 命名空间声明后面（但在命名空间内），定义一个名为 `IEchoContract` 的新接口，然后将 `ServiceContractAttribute` 属性应用于该接口，命名空间值为 `http://samples.microsoft.com/ServiceModel/Relay/`。 该命名空间值不同于在整个代码范围内使用的命名空间。 相反，该命名空间值将用作此协定的唯一标识符。 显式指定命名空间可防止将默认的命名空间值添加到约定名称中。 在命名空间声明后，粘贴以下代码：

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > 通常情况下，服务协定命名空间包含一个包括版本信息的命名方案。 服务协定命名空间中包括的版本信息可以使服务通过将新服务协定定义为新命名空间并将其公开到新的终结点上，来隔离重大更改。 以这种方式，客户端可以继续使用旧的服务协定，而无需进行更新。 版本信息可能包含日期或内部版本号。 有关详细信息，请参阅 [服务版本控制](http://go.microsoft.com/fwlink/?LinkID=180498)。 鉴于此教程的目的，服务协定命名空间的命名方案不包含版本信息。
   >
   >
8. 在 `IEchoContract` 接口中，为 `IEchoContract` 协定在接口中公开的单个操作声明一个方法，然后将 `OperationContractAttribute` 属性应用到希望将其作为公共 WCF 中继协定的一部分进行公开的方法中，如下所示：

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. 直接在 `IEchoContract` 接口定义之后，声明从 `IEchoContract` 中继承，并可传承到 `IClientChannel` 接口的通道，如下所示：

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    通道是主机和客户端用来互相传递信息的 WCF 对象。 随后，将针对通道编写代码，以在两个应用程序之间回显信息。
10. 在“生成”菜单中，单击“生成解决方案”或按 **Ctrl+Shift+B** 以确认到目前为止操作的准确性。

### <a name="example"></a>示例

以下代码显示了定义 WCF 中继协定的基本接口。

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

既然已创建接口，可以实现该接口。

## <a name="implement-the-wcf-contract"></a>实现 WCF 协定

创建 Azure 中继首先需要创建使用接口定义的协定。 有关创建接口的详细信息，请参阅上一步。 下一步是实现该接口。 此步骤包括创建名为 `EchoService` 的类，用于实现用户定义的 `IEchoContract` 接口。 实现接口后，即可使用 App.config 配置文件配置接口。 该配置文件包含应用程序所需的信息，如服务的名称、协定的名称，以及用来与中继服务通信的协议类型。 该过程后面的示例中提供了这些任务所用的代码。 有关如何实现服务协定的更多常规讨论，请参阅 WCF 文档中的 [实现服务协定](https://msdn.microsoft.com/library/ms733764.aspx) 。

1. 在 `IEchoContract` 接口定义的正下方创建名为 `EchoService` 的新类。 `EchoService` 类实现 `IEchoContract` 接口。

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    与其他接口实现类似，可以在另一个文件中实现定义。 但是，在本教程中，实现所在的文件与接口定义和 `Main` 方法所在的文件相同。
2. 将 [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) 属性应用于 `IEchoContract` 接口。 该属性指定服务名称和命名空间。 完成后，`EchoService` 类将如下所示：

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. 在 `EchoService` 类中，实现 `IEchoContract` 接口中定义的 `Echo` 方法。

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. 单击“生成”，并单击“生成解决方案”以确认工作的准确性。

### <a name="define-the-configuration-for-the-service-host"></a>定义服务主机的配置

1. 配置文件非常类似于 WCF 配置文件。 该配置文件包括服务名称、终结点（即，Azure 中继公开的、让客户端和主机相互通信的位置）和绑定（用于通信的协议类型）。 此处的主要差别在于，配置的服务终结点是指 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) 绑定，它不是 .NET Framework 的一部分。 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) 是由服务定义的绑定之一。
2. 在 **解决方案资源管理器**中，双击 App.config 文件以在 Visual Studio 编辑器中将其打开。
3. 在 `<appSettings>` 元素中，将占位符替换为服务命名空间的名称以及在先前步骤中复制的 SAS 密钥。
4. 在 `<system.serviceModel>` 标记中，添加 `<services>` 元素。 可以在单个配置文件中定义多个中继应用程序。 但是，本教程只定义一个。

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. 在 `<services>` 元素中，添加 `<service>` 元素来定义服务名称。

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. 在 `<service>` 元素中，定义终结点协定的位置，以及终结点绑定的类型。

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    终结点用于定义客户端会在何处查找主机应用程序。 接下来，本教程使用此步骤创建一个通过 Azure 中继完全公开主机的 URI。 绑定声明我们将 TCP 用作协议，以与中继服务进行通信。
7. 在“生成”菜单中，单击“生成解决方案”以确认工作的准确性。

### <a name="example"></a>示例

下面的代码显示服务协定的实现。

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

以下代码显示了与该服务主机关联的 App.config 文件的基本格式。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-the-relay-service"></a>托管并运行基本 Web 服务以向中继服务注册

此步骤介绍如何运行 Azure 中继服务。

### <a name="create-the-relay-credentials"></a>创建中继凭据

1. 在 `Main()`中，创建两个变量，将命名空间和从控制台窗口中读取的 SAS 密钥存储在其中。

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    随后将使用 SAS 密钥来访问项目。 命名空间作为参数传递给 `CreateServiceUri` 以创建服务 URI。
2. 使用 [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) 对象声明使用 SAS 密钥作为凭据类型。 在最后一步中添加的代码后直接添加以下代码。

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>为服务创建基本地址

在上一个步骤添加的代码后面，为服务的基址创建 `Uri` 实例。 此 URI 指定服务总线方案、命名空间，以及服务接口的路径。

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" 是服务总线方案的缩写，并指示我们正在使用 TCP 作为协议。 先前当 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 被指定为绑定时，在配置文件中也指示了这一点。

对于本教程中，URI 是 `sb://putServiceNamespaceHere.windows.net/EchoService`。

### <a name="create-and-configure-the-service-host"></a>创建并配置服务主机

1. 将连接模式设置为 `AutoDetect`

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    连接模式描述服务用于与中继服务进行通信的协议；连接模式为 HTTP 或 TCP。 使用默认设置 `AutoDetect`，服务尝试通过 TCP（如果可用）或 HTTP（如果 TCP 不可用）连接到 Azure 中继。 请注意这与服务为客户端通信指定的协议不同。 为客户端通信指定的协议由所使用的绑定所决定。 例如，服务可以使用指定其终结点的 [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) 绑定通过 HTTP 与客户端通信。 同一个服务可以指定 **ConnectivityMode.AutoDetect**，以便服务通过 TCP 与 Azure 中继通信。
2. 使用之前在本部分中创建的 URI 创建服务主机。

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    该服务主机是可实例化服务的 WCF 对象。 在这里传递想要创建的服务类型（`EchoService` 类型），以及想要公开服务的地址。
3. 在 Program.cs 文件的顶部，添加对 [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) 和 [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description) 的引用。

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. 返回到 `Main()`，配置终结点以启用公开访问。

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    此步骤告知中继服务可以通过检查项目的 ATOM 源公开找到应用程序。 如果将 **DiscoveryType** 设置为 **private**，客户端将仍能够访问该服务。 但是，当搜索中继命名空间时不会显示该服务。 相反，客户端必须事先知道终结点路径。
5. 将服务凭据应用到 App.config 文件中定义的服务终结点：

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    如在上一步中所述，可能已经在配置文件中声明多个服务和终结点。 如果已配置，此代码将遍历配置文件并且搜索可能应用了凭据的每个终结点。 但是，对于本教程中，配置文件只有一个终结点。

### <a name="open-the-service-host"></a>打开服务主机

1. 打开服务。

    ```csharp
    host.Open();
    ```
2. 通知用户该服务正在运行，并说明如何关闭服务。

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. 完成后，关闭服务主机。

    ```csharp
    host.Close();
    ```
4. 按 **Ctrl+Shift+B** 生成项目。

### <a name="example"></a>示例

完成的服务代码应如下所示。 代码包括本教程中前面步骤中使用的服务协定和实现，并将服务托管在控制台应用程序中。

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>创建服务协定的 WCF 客户端

下一步将创建客户端应用程序，并定义会在后续步骤中实现的服务协定。 请注意，许多这样的步骤类似于创建服务的步骤：定义协定、编辑 App.config 文件、使用凭据连接到中继服务等。 该过程后面的示例中提供了这些任务所用的代码。

1. 通过执行以下操作为客户端通在当前 Visual Studio 解决方案中创建一个新的项目：

   1. 在解决方案资源管理器中，在包含该服务的同一解决方案中，右键单击当前解决方案（不是项目），并单击“添加”。 然后，单击“新建项目”。
   2. 在“添加新项目”对话框中，单击“Visual C#”（如果未显示“Visual C#”，则在“其他语言”下方查看），再选择“控制台应用 (.NET Framework)”模板，并将其命名为“EchoClient”。
   3. 单击 **“确定”**。
      <br />
2. 在解决方案资源管理器中，双击 **EchoClient** 项目中的 Program.cs 文件以在编辑器中将其打开（如果尚未打开）。
3. 将命名空间名称从其默认名称 `EchoClient` 更改为 `Microsoft.ServiceBus.Samples`。
4. 安装[服务总线 NuGet 包](https://www.nuget.org/packages/WindowsAzure.ServiceBus)：在解决方案资源管理器中，右键单击“EchoClient”项目，并单击“管理 NuGet 包”。 单击“浏览”选项卡，并搜索 `Microsoft Azure Service Bus`。 单击“安装” 并接受使用条款。

    ![][3]
5. 为 Program.cs 文件中的 [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) 命名空间添加 `using` 语句。

    ```csharp
    using System.ServiceModel;
    ```
6. 如下面的示例中所示，将服务协定定义添加到命名空间。 请注意，此定义等同于“服务”项目中所使用的定义。 `Microsoft.ServiceBus.Samples` 命名空间的顶部。

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. 按 **Ctrl+Shift+B** 生成客户端。

### <a name="example"></a>示例

下面的代码显示了 **EchoClient** 项目中的 Program.cs 文件的当前状态。

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>配置 WCF 客户端

在此步骤中，可以为之前在本教程中创建的访问服务的基本客户端应用程序创建 App.config 文件。 此 App.config 文件用于定义终结点的协定、绑定和名称。 该过程后面的示例中提供了这些任务所用的代码。

1. 在解决方案资源管理器的 **EchoClient** 项目中，双击“App.config”以在 Visual Studio 编辑器中打开该文件。
2. 在 `<appSettings>` 元素中，将占位符替换为服务命名空间的名称以及在先前步骤中复制的 SAS 密钥。
3. 在 system.serviceModel 元素中，添加 `<client>` 元素。

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    这一步声明，要定义的是 WCF 式客户端应用程序。
4. 在 `client` 元素中，定义终结点的名称、协定和绑定类型。

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    此步骤定义终结点的名称、服务中定义的协定，以及客户端应用程序使用 TCP 与 Azure 中继进行通信的事实。 终结点名称在下一步中用于将此终结点配置与服务 URI 链接。
5. 单击“文件”，并单击“全部保存”。

## <a name="example"></a>示例

下面的代码显示了 Echo 客户端的 App.config 文件。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>实现 WCF 客户端
在此步骤中，实现了可访问之前在本教程中创建的服务的基本客户端应用程序。 与服务相似，该客户端执行许多相同的操作访问 Azure 中继：

1. 设置连接模式。
2. 创建用于定位主机服务的 URI。
3. 定义安全凭据。
4. 将凭据应用到连接。
5. 打开连接。
6. 执行应用程序特定的任务。
7. 关闭连接。

但是，主要的区别之一在于，客户端应用程序使用通道连接到中继服务，而服务则使用对 **ServiceHost** 的调用。 该过程后面的示例中提供了这些任务所用的代码。

### <a name="implement-a-client-application"></a>实现客户端应用程序
1. 将连接模式设置为 **AutoDetect**。 在 **EchoClient** 应用程序的 `Main()` 方法中添加以下代码。

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. 定义变量以保存用于服务命名空间的值，以及从控制台读取的 SAS 密钥。

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. 创建用于定义中继项目中主机位置的 URI。

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. 创建服务命名空间终结点的凭据对象。

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. 创建加载在 App.config 文件中所述的配置的通道工厂。

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    通道工厂是创建通道（通过该通道，服务和客户端可以进行通信）的一个 WCF 对象。
6. 应用凭据。

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. 创建并打开服务通道。

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. 编写用于回显的基本用户界面和功能。

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    请注意，代码使用通道对象的实例作为服务代理。
9. 关闭通道，并关闭工厂。

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

## <a name="example"></a>示例

完成的代码应如下所示，演示了如何创建客户端应用程序、如何调用服务操作以及如何在完成操作调用后关闭客户端。

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>运行应用程序

1. 按 **Ctrl+Shift+B** 生成解决方案。 这会生成在先前步骤中创建的客户端项目和服务项目。
2. 在运行客户端应用程序之前，必须确保服务应用程序正在运行。 在 Visual Studio 的解决方案资源管理器中，右键单击“EchoService”解决方案，并单击“属性”。
3. 在“解决方案属性”对话框中，单击“启动项目”，并单击“多启动项目”按钮。 **EchoService** 显示在列表的最前面。
4. 将 **EchoService** 和 **EchoClient** 项目的“操作”框设置为“启动”。

    ![][5]
5. 单击“项目依赖项” 。 在“项目”框中，选择“EchoClient”。 在“依赖于”框中，确保选中“EchoService”。

    ![][6]
6. 单击“确定”，关闭“属性”对话框。
7. 按 **F5** 运行这两个项目。
8. 此时会打开两个控制台窗口并提示输入命名空间名称。 必须先运行服务，因此在“EchoService”控制台窗口中输入命名空间，并按“Enter”。
9. 接下来，会提示提供 SAS 密钥。 输入 SAS 密钥并按“ENTER”。

    以下是来自控制台窗口的示例输出。 请注意，此处提供的值仅限于示例目的。

    `Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

    服务应用程序将其正在侦听的地址打印到控制台窗口中，如下面的示例中所示。

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
10. 在 **EchoClient** 控制台窗口中，输入之前为服务应用程序输入的相同信息。 请按照前面的步骤，为客户端应用程序输入相同的服务命名空间和 SAS 密钥值。
11. 输入这些值后，客户端将打开服务通道并提示输入如以下控制台输出示例中所示的某些文本。

    `Enter text to echo (or [Enter] to exit):`

    输入将发送到服务应用程序的某些文本，并按“Enter”。 此文本通过 Echo 服务操作发送到服务并显示在服务控制台窗口中，如下面的示例输出所示。

    `Echoing: My sample text`

    客户端应用程序接收 `Echo` 操作的返回值（此为原始文本），并将其打印到控制台窗口。 以下是来自客户端控制台窗口的示例输出。

    `Server echoed: My sample text`
12. 可以继续以这种方式将来自客户端的短信发送至服务。 完成后，在客户端和服务控制台窗口中按 Enter 以结束这两个应用程序。

## <a name="next-steps"></a>后续步骤

本教程介绍如何使用服务总线的 WCF 中继功能，构建 Azure 中继客户端应用程序和服务。 有关使用[服务总线消息传送](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging)的类似教程，请参阅[服务总线队列入门](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。

若要了解有关 Azure 中继的详细信息，请参阅以下主题。

* [Azure 服务总线体系结构概述](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Azure 中继概述](relay-what-is-it.md)
* [如何通过 .NET 使用 WCF 中继服务](relay-wcf-dotnet-get-started.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
