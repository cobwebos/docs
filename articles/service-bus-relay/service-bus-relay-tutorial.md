---
title: 使用 Azure WCF 中继向外部客户端公开本地 WCF REST 服务 | Microsoft Docs
description: 使用 WCF 中继构建客户端和服务应用程序。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: 4707e56a09c257c9e03e6db070083c81ffde07b6
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212899"
---
# <a name="expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>使用 Azure WCF 中继向外部客户端公开本地 WCF REST 服务

本教程介绍如何使用 Azure 中继构建 WCF 中继客户端应用程序和服务。 有关使用[服务总线消息传送](../service-bus-messaging/service-bus-messaging-overview.md)的类似教程，请参阅[服务总线队列入门](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。

完成本教程后，你将了解创建 WCF 中继的客户端和服务应用程序的步骤。 服务是公开一个或多个终结点的构造，就像它们的原始 WCF 对应项。 每个终结点都公开一个或多个服务操作。 服务的终结点用于指定可在其中找到服务的地址、包含客户端必须与服务进行通信的信息的绑定，以及定义服务向其客户端提供的功能的协定。 WCF 和 WCF 中继之间的主要区别在于：终结点在云中公开，而不是在本地计算机中公开。

完成本教程中的一系列章节后，你将有一个正在运行的服务。 你还将拥有可以调用服务操作的客户端。 

在本教程中执行以下任务：

> [!div class="checklist"]
>
> * 安装本教程的必备组件。
> * 创建中继命名空间。
> * 创建 WCF 服务协定。
> * 实现 WCF 协定。
> * 承载并运行 WCF 服务以向中继服务注册。
> * 创建服务协定的 WCF 客户端。
> * 配置 WCF 客户端。
> * 实现 WCF 客户端。
> * 运行应用程序。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* [Visual Studio 2015 或更高版本](https://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2019。
* 用于 .NET 的 Azure SDK。 从 [SDK 下载页](https://azure.microsoft.com/downloads/)安装它。

## <a name="create-a-relay-namespace"></a>创建中继命名空间

第一步是创建命名空间并获取[共享访问签名 (SAS)](../service-bus-messaging/service-bus-sas.md) 密钥。 命名空间为每个通过中继服务公开的应用程序提供应用程序边界。 创建服务命名空间时，系统会自动生成一个 SAS 密钥。 服务命名空间与 SAS 密钥的组合为 Azure 提供了一个用于验证应用程序访问权限的凭据。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>定义 WCF 服务协定

服务协定指定服务支持的操作。 操作是 web 服务方法或函数。 约定通过定义 C++、C# 或 Visual Basic 接口来创建。 接口中的每个方法都对应一个特定的服务操作。 必须将 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 属性应用于每个接口，并且必须将 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 属性应用于每个操作。 如果具有[ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute)属性的接口中的方法没有[OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute)属性，则该方法不会公开。 该过程后面的示例中提供了这些任务的代码。 有关协定和服务的更多讨论，请参阅[设计和实现服务](/dotnet/framework/wcf/designing-and-implementing-services)。

### <a name="create-a-relay-contract-with-an-interface"></a>使用接口创建中继协定

1. 以管理员身份启动 Microsoft Visual Studio。 为此，请右键单击 Visual Studio 程序图标，然后选择 "以**管理员身份运行**"。
1. 在 Visual Studio 中，选择“新建项目”。
1. 在 "**创建新项目**" 中，选择 "**控制台应用（.NET Framework）** "， C#然后选择 "**下一步**"。
1. 将项目命名为*EchoService* ，然后选择 "**创建**"。

   ![创建控制台应用][2]

1. 在“解决方案资源管理器”中，右键单击项目，并选择“管理 NuGet 包”。 在**NuGet 包管理器**中，选择 "**浏览**"，搜索并选择 " **windowsazure.storage**"。 选择 "**安装**"，并接受使用条款。

    ![服务总线包][3]

   此包自动添加对服务总线库和 WCF `System.ServiceModel`的引用。 [System.ServiceModel](/dotnet/api/system.servicemodel) 是可以以编程方式访问 WCF 基本功能的命名空间。 服务总线使用 WCF 的许多对象和属性来定义服务约定。

1. 在 Program.cs 的`using`顶部添加以下语句：

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. 将命名空间名称从其默认名称 `EchoService` 更改为 `Microsoft.ServiceBus.Samples`。

   > [!IMPORTANT]
   > 本教程使用C#命名空间`Microsoft.ServiceBus.Samples` ，该命名空间是在配置[WCF 客户端](#configure-the-wcf-client)部分的配置文件中使用的基于协定的托管类型的命名空间。 生成此示例时，可以指定所需的任何命名空间。 但是，除非随后在应用程序配置文件中修改协定和服务的命名空间，否则教程将不起作用。 *App.config*文件中指定的命名空间必须与在C#文件中指定的命名空间相同。
   >

1. 直接在 `Microsoft.ServiceBus.Samples` 命名空间声明后面（但在命名空间内），定义一个名为 `IEchoContract` 的新接口，然后将 `ServiceContractAttribute` 属性应用于该接口，命名空间值为 `https://samples.microsoft.com/ServiceModel/Relay/`。 在命名空间声明后，粘贴以下代码：

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    该命名空间值不同于在整个代码范围内使用的命名空间。 相反，该命名空间值将用作此协定的唯一标识符。 显式指定命名空间可防止将默认的命名空间值添加到约定名称中。

   > [!NOTE]
   > 通常情况下，服务协定命名空间包含一个包括版本信息的命名方案。 服务协定命名空间中包括的版本信息可以使服务通过将新服务协定定义为新命名空间并将其公开到新的终结点上，来隔离重大更改。 以这种方式，客户端可以继续使用旧的服务协定，而无需进行更新。 版本信息可能包含日期或内部版本号。 有关详细信息，请参阅 [服务版本控制](/dotnet/framework/wcf/service-versioning)。 对于本教程，服务协定命名空间的命名方案不包含版本信息。
   >

1. 在 `IEchoContract` 接口中，为 `IEchoContract` 协定在接口中公开的单个操作声明一个方法，然后将 `OperationContractAttribute` 属性应用到希望将其作为公共 WCF 中继协定的一部分进行公开的方法中，如下所示：

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. 直接在 `IEchoContract` 接口定义之后声明从 `IEchoContract` 中继承并同样继承到 `IClientChannel` 接口的通道，如下所示：

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    通道是主机和客户端用来互相传递信息的 WCF 对象。 稍后，你将针对通道编写代码，以在两个应用程序之间回显信息。

1. 选择 "**生成** > " "生成**解决方案**" 或按 "Ctrl + Shift + B" 确认工作的准确性。

### <a name="example-of-a-wcf-contract"></a>WCF 协定示例

以下代码显示了定义 WCF 中继协定的基本接口。

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

创建 Azure 中继要求首先使用接口创建协定。 有关创建接口的详细信息，请参阅上一节。 下一个过程实现接口。 此任务涉及到创建一个名`EchoService`为的类，该类实现`IEchoContract`用户定义的接口。 实现接口后，可以使用*app.config*配置文件配置接口。 配置文件包含应用程序所需的信息。 此信息包括服务的名称、协定的名称，以及用于与中继服务通信的协议类型。 该过程后面的示例中提供了用于这些任务的代码。 有关如何实现服务协定的更多常规讨论，请参阅[实现服务](/dotnet/framework/wcf/implementing-service-contracts)协定。

1. 在 `IEchoContract` 接口定义的正下方创建名为 `EchoService` 的新类。 `EchoService` 类实现 `IEchoContract` 接口。

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    与其他接口实现类似，可以在另一个文件中实现定义。 但是，在本教程中，实现所在的文件与接口定义和 `Main()` 方法所在的文件相同。

1. 将 [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) 属性应用于 `IEchoContract` 接口。 该属性指定服务名称和命名空间。 完成后，`EchoService` 类将如下所示：

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. 在 `EchoService` 类中，实现 `IEchoContract` 接口中定义的 `Echo` 方法。

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. 选择 "**生成** > " "生成**解决方案**" 或选择 "Ctrl + Shift + B"。

### <a name="define-the-configuration-for-the-service-host"></a>定义服务主机的配置

配置文件类似于 WCF 配置文件。 它包括服务名称、终结点和绑定。 终结点是 Azure 中继公开的位置，供客户端和主机相互通信。 绑定是用于通信的协议类型。 主要区别在于，此配置的服务终结点是指[NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)绑定，它不是 .NET Framework 的一部分。 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) 是由服务定义的绑定之一。

1. 在**解决方案资源管理器**中，双击 " **app.config** " 以在 Visual Studio 编辑器中打开该文件。
1. 在 `<appSettings>` 元素中，将占位符替换为服务命名空间的名称以及在先前步骤中复制的 SAS 密钥。
1. 在 `<system.serviceModel>` 标记中，添加 `<services>` 元素。 可以在单个配置文件中定义多个中继应用程序。 但是，本教程只定义一个。

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. 在 `<services>` 元素中，添加 `<service>` 元素来定义服务名称。

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. 在 `<service>` 元素中，定义终结点协定的位置，以及终结点绑定的类型。

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    终结点用于定义客户端会在何处查找主机应用程序。 接下来，本教程使用此步骤创建一个通过 Azure 中继完全公开主机的 URI。 绑定声明我们正在将 TCP 用作协议，以与中继服务进行通信。

1. 选择 "**生成** > " "生成**解决方案**" 或按 "Ctrl + Shift + B" 确认工作的准确性。

### <a name="example-of-implementation-of-a-service-contract"></a>服务协定的实现示例

下面的代码显示服务协定的实现。

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

以下代码显示了与服务主机*关联的 app.config 文件的*基本格式。

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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>托管并运行 WCF 服务以向中继服务注册

此步骤介绍如何运行 Azure 中继服务。

### <a name="create-the-relay-credentials"></a>创建中继凭据

1. 在 `Main()`中，创建两个变量，将命名空间和从控制台窗口中读取的 SAS 密钥存储在其中。

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    随后将使用 SAS 密钥来访问你的项目。 命名空间作为参数传递给 `CreateServiceUri` 以创建服务 URI。

1. 使用[TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior)对象声明将使用 SAS 密钥作为凭据类型。 在最后一步中添加的代码后直接添加以下代码。

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>为服务创建基本地址

在上一部分中添加的代码后面，为服务的`Uri`基址创建一个实例。 此 URI 指定服务总线方案、命名空间，以及服务接口的路径。

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

值 "sb" 是服务总线方案的缩写。 它表示我们使用 TCP 作为协议。 当[NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)指定为绑定时，配置文件中也会显示此方案。

对于本教程中，URI 是 `sb://putServiceNamespaceHere.windows.net/EchoService`。

### <a name="create-and-configure-the-service-host"></a>创建并配置服务主机

1. 仍在中`Main()`工作，请将连接模式`AutoDetect`设置为。

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    连接模式描述服务用于与中继服务进行通信的协议；连接模式为 HTTP 或 TCP。 使用默认设置`AutoDetect`，服务尝试通过 tcp （如果可用）连接到 Azure 中继（如果 tcp 不可用）。 此结果与服务为客户端通信指定的协议不同。 为客户端通信指定的协议由所使用的绑定所决定。 例如，服务可以使用指定其终结点的 [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) 绑定通过 HTTP 与客户端通信。 该服务可以指定`ConnectivityMode.AutoDetect` ，以便服务通过 TCP 与 Azure 中继通信。

1. 使用之前在本部分中创建的 URI 创建服务主机。

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    该服务主机是可实例化服务的 WCF 对象。 在此，您将向其传递您要创建的服务类型、 `EchoService`类型，以及要公开服务的地址。

1. 在 Program.cs 文件的顶部，添加对[system.servicemodel. description](/dotnet/api/system.servicemodel.description)和的[引用。](/dotnet/api/microsoft.servicebus.description)

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. 返回到 `Main()`，配置终结点以启用公开访问。

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    此步骤告知中继服务，可以通过检查项目的 Atom 源公开找到应用程序。 如果将设置`DiscoveryType`为`private`，则客户端仍可以访问服务。 但是，该服务在搜索`Relay`命名空间时不会显示。 相反，客户端必须事先知道终结点路径。

1. 将服务凭据应用到*app.config*文件中定义的服务终结点：

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    如前文所述，可以在配置文件中声明多个服务和终结点。 如果已配置，此代码将遍历配置文件并且搜索可能应用了凭据的每个终结点。 对于本教程，配置文件只有一个终结点。

### <a name="open-the-service-host"></a>打开服务主机

1. 仍在`Main()`中，添加以下行以打开服务。

    ```csharp
    host.Open();
    ```

1. 通知用户该服务正在运行，并说明如何关闭服务。

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. 完成后，关闭服务主机。

    ```csharp
    host.Close();
    ```

1. 按 Ctrl + Shift + B 生成项目。

### <a name="example-that-hosts-a-service-in-a-console-application"></a>在控制台应用程序中承载服务的示例

完成的服务代码应如下所示。 代码包括本教程中前面步骤中使用的服务协定和实现，并将服务托管在控制台应用程序中。

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

下一个任务是创建客户端应用程序并定义稍后要实现的服务协定。 这些步骤类似于用于创建服务的步骤：定义协定、编辑*app.config*文件、使用凭据连接到中继服务等。 该过程后面的示例中提供了这些任务所用的代码。

1. 在当前 Visual Studio 解决方案中为客户端创建一个新项目：

   1. 在**解决方案资源管理器**中，右键单击当前解决方案（不是项目），然后选择 "**添加** > **新项目**"。
   1. 在 "**添加新项目**" 中，选择C#"**控制台应用（.NET Framework）** "，然后选择 "**下一步**"。
   1. 将项目命名为*EchoClient* ，然后选择 "**创建**"。

1. 在**解决方案资源管理器**的 " **EchoClient** " 项目中，双击 " **Program.cs** " 以在编辑器中打开该文件（如果该文件尚未打开）。
1. 将命名空间名称从其默认名称 `EchoClient` 更改为 `Microsoft.ServiceBus.Samples`。
1. 安装[服务总线 NuGet 包](https://www.nuget.org/packages/WindowsAzure.ServiceBus)：

   1. 在**解决方案资源管理器**中，右键单击**EchoClient** ，然后选择 "**管理 NuGet 包**"。
   1. 选择 "**浏览**"，搜索并选择 " **windowsazure.storage**"。 选择 "**安装**"，并接受使用条款。

      ![安装 service bus 包][4]

1. 为*Program.cs*文件中的[system.servicemodel](/dotnet/api/system.servicemodel)命名空间添加语句。`using`

    ```csharp
    using System.ServiceModel;
    ```

1. 如下面的示例中所示，将服务协定定义添加到命名空间。 此定义与在**服务**项目中使用的定义相同。 将此代码添加到`Microsoft.ServiceBus.Samples`命名空间的顶部。

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. 按 Ctrl + Shift + B 生成客户端。

### <a name="example-of-the-echoclient-project"></a>EchoClient 项目的示例

下面的代码显示了**EchoClient**项目中的*Program.cs*文件的当前状态。

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

在此步骤中，你将为基本客户端应用程序创建*app.config*文件，该应用程序访问之前在本教程中创建的服务。 此*app.config*文件用于定义终结点的协定、绑定和名称。 该过程后面的示例中提供了这些任务所用的代码。

1. 在**解决方案资源管理器**的 " **EchoClient** " 项目中，双击 " **App.config** " 以在 Visual Studio 编辑器中打开该文件。
1. 在 `<appSettings>` 元素中，将占位符替换为服务命名空间的名称以及在先前步骤中复制的 SAS 密钥。
1. 在元素中，添加一个`<client>`元素。 `system.serviceModel`

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    此代码声明你正在定义一个 WCF 样式的客户端应用程序。

1. 在 `client` 元素中，定义终结点的名称、协定和绑定类型。

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    此代码定义终结点的名称。 它还定义在服务中定义的协定，以及客户端应用程序使用 TCP 与 Azure 中继进行通信的事实。 终结点名称在下一步中用于将此终结点配置与服务 URI 链接。

1. 选择 "**文件** > " "**全部保存**"。

### <a name="example-of-the-appconfig-file"></a>App.config 文件的示例

下面的代码显示了 Echo 客户*端的 app.config 文件。*

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

在本部分中，你将实现一个基本客户端应用程序，该应用程序可访问之前在本教程中创建的服务。 与服务相似，该客户端执行许多相同的操作访问 Azure 中继：

* 设置连接模式。
* 创建用于定位主机服务的 URI。
* 定义安全凭据。
* 将凭据应用到连接。
* 打开连接。
* 执行应用程序特定的任务。
* 关闭连接。

但是，主要区别之一是客户端应用程序使用通道连接到中继服务。 服务使用对**ServiceHost**的调用。 该过程后面的示例中提供了这些任务所用的代码。

### <a name="implement-a-client-application"></a>实现客户端应用程序

1. 将连接模式设置为 `AutoDetect` 在 **EchoClient** 应用程序的 `Main()` 方法中添加以下代码。

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. 定义变量以保存用于服务命名空间的值，以及从控制台读取的 SAS 密钥。

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. 创建用于定义中继项目中主机位置的 URI。

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. 创建服务命名空间终结点的凭据对象。

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. 创建加载*app.config*文件中所述的配置的通道工厂。

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    通道工厂是创建通道（通过该通道，服务和客户端可以进行通信）的一个 WCF 对象。

1. 应用凭据。

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. 创建并打开服务通道。

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. 编写用于回显的基本用户界面和功能。

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

    此代码使用通道对象的实例作为服务的代理。

1. 关闭通道，并关闭工厂。

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>本教程的示例代码

已完成的代码应如下所示。 此代码演示如何创建客户端应用程序、如何调用服务的操作以及如何在完成操作调用后关闭客户端。

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

1. 按 Ctrl + Shift + B 生成解决方案。 此操作将生成在前面的步骤中创建的客户端项目和服务项目。
1. 在运行客户端应用程序之前，必须确保服务应用程序正在运行。 在**解决方案资源管理器**中，右键单击**EchoService**解决方案，然后选择 "**属性**"。
1. 在**属性页**中，选择 "**通用属性** > " "**启动项目**"，然后选择**多个启动项目**。 **EchoService** 显示在列表的最前面。
1. 将 **EchoService** 和 **EchoClient** 项目的“操作”框设置为“启动”。

    ![项目属性页][5]

1. 选择 "**项目依赖项**"。 在**项目**中，选择 " **EchoClient**"。 对于 "**依赖于**"，请确保选择 " **EchoService** "。

    ![项目依赖项][6]

1. 选择 **"确定"** 关闭**属性页**。
1. 选择 F5 以运行这两个项目。
1. 此时会打开两个控制台窗口并提示输入命名空间名称。 必须先运行服务，因此在**EchoService**控制台窗口中输入命名空间，然后选择 enter。
1. 接下来，控制台会提示你提供 SAS 密钥。 输入 SAS 密钥，然后选择 "Enter"。

    以下是来自控制台窗口的示例输出。 此处的值只是示例。

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    服务应用程序将其正在侦听的地址打印到控制台窗口中，如下面的示例中所示。

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. 在 **EchoClient** 控制台窗口中，输入之前为服务应用程序输入的相同信息。 为客户端应用程序输入相同的服务命名空间和 SAS 密钥值。
1. 输入这些值后，客户端将打开服务通道并提示输入如以下控制台输出示例中所示的某些文本。

    `Enter text to echo (or [Enter] to exit):`

    输入要发送到服务应用程序的某些文本，然后选择 "Enter"。 此文本通过 Echo 服务操作发送到服务并显示在服务控制台窗口中，如下面的示例输出所示。

    `Echoing: My sample text`

    客户端应用程序接收 `Echo` 操作的返回值（此为原始文本），并将其打印到控制台窗口。 以下文本是客户端控制台窗口的示例输出。

    `Server echoed: My sample text`

1. 可以继续以这种方式将来自客户端的短信发送至服务。 完成后，在客户端和服务控制台窗口中选择 Enter 以结束这两个应用程序。

## <a name="next-steps"></a>后续步骤

转到以下教程：

> [!div class="nextstepaction"]
>[向网络外部的客户端公开本地 WCF REST 服务](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
