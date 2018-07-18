---
title: 开始在 .NET 中使用 Azure Relay WCF 中继 | Microsoft Docs
description: 了解如何使用 Azure Relay WCF 中继连接两个托管于不同位置的应用程序。
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: face684190456fbf4b78a84ac3afe7a4ead8995a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697886"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>如何将 Azure Relay WCF 中继与 .NET 配合使用
本文介绍如何使用 Azure 中继服务。 相关示例用 C# 编写并使用服务总线程序集中包含的 Windows Communication Foundation (WCF) API 及扩展。 有关 Azure 中继的详细信息，请参阅 [Azure 中继概述](relay-what-is-it.md)。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>什么是 WCF 中继？

通过 Azure [WCF 中继](relay-what-is-it.md)服务可构建在 Azure 数据中心和自己的本地企业环境中运行的混合应用程序。 中继服务可简化这一过程，它允许安全地向公有云公开位于企业网络内的 Windows Communication Foundation (WCF) 服务，而无需打开防火墙连接，也无需对企业网络基础结构进行彻底的更改。

![WCF 中继概念](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Azure 中继可用于在现有企业环境中托管 WCF 服务。 然后，可以将侦听传入会话和请求这些 WCF 服务的任务委托给在 Azure 内运行的中继服务。 这使你能够向 Azure 中运行的应用程序代码或者向移动工作者或 Extranet 合作伙伴环境公开这些服务。 中继允许精确、安全地控制谁可以访问这些服务。 它提供了一种强大且安全的方式，从现有企业解决方案公开应用程序功能和数据并从云中利用这些功能和数据。

本文探讨如何使用 Azure 中继创建 WCF Web 服务，并使用可实现双方之间安全对话的 TCP 通道绑定公开该服务。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>获取服务总线 NuGet 包
[服务总线 NuGet 包](https://www.nuget.org/packages/WindowsAzure.ServiceBus) 是获取服务总线 API 并为应用程序配置所有服务总线依赖项的最简单的方法。 若要在项目中安装 NuGet 包，请执行以下操作：

1. 在“解决方案资源管理器”中，右键单击“引用”，并单击“管理 NuGet 包”。
2. 搜索“服务总线”并选择“Microsoft Azure 服务总线”项。 **安装** ”以完成安装，并关闭以下对话框：
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>通过 TCP 公开和使用 SOAP Web 服务
要公开现有 WCF SOAP Web 服务以供外部使用，必须更改服务绑定和地址。 这可能需要更改配置文件或者可能需要更改代码，具体取决于如何设置和配置 WCF 服务。 请注意，WCF 允许对同一服务使用多个网络终结点，因此，可以在添加中继终结点以便进行外部访问的同时保留现有内部终结点。

在此任务中，将构建一个简单的 WCF 服务并向其添加中继侦听程序。 此练习假定你熟悉 Visual Studio，因此将不演练创建项目的所有详细信息。 而是侧重于代码。

在开始执行这些步骤之前，请完成以下过程以设置环境：

1. 在 Visual Studio 中，在解决方案内创建一个包含以下两个项目的控制台应用程序：“客户端”和“服务”。
2. 向这两个项目添加服务总线 NuGet 包。 此程序包将向项目添加所有必需的程序集引用。

### <a name="how-to-create-the-service"></a>如何创建服务
首先，创建该服务本身。 任何 WCF 服务都包含至少三个不同部分：

* 描述交换哪些信息以及将调用哪些操作的协定的定义。
* 上述协定的实施方案。
* 托管 WCF 服务并公开多个终结点的主机。

本部分中的代码示例涵盖了其中的每个组成部分。

协定定义用于添加两个数字并返回相应结果的单个操作 `AddNumbers`。 `IProblemSolverChannel` 接口使客户端能够更轻松地管理代理生存期。 创建这样一个接口被认为是最佳实践。 最好将此协定定义放入单独的文件中，以便可从“客户端”和“服务”两个项目中引用该文件，但也可以将代码复制到这两个项目中。

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

协定到位后，可以按以下方式进行实施：

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>以编程方式配置服务主机
协定和实施完成后，现在就可以托管服务了。 托管发生在 [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx) 对象内，该对象负责管理服务实例并托管侦听消息的终结点。 以下代码使用常规的本地终结点和中继终结点配置服务，以便并列展示内部和外部终结点的外观。 将字符串 namespace 替换为命名空间名称，并将 yourKey 替换为前面的设置步骤中获取的 SAS 密钥。

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

在本示例中，将创建两个位于同一协定实施中的终结点。 一个是本地的，一个通过 Azure 中继进行投影。 两者之间的主要区别是绑定；本地终结点使用 [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx)，而中继终结点和地址使用 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding)。 本地终结点有一个使用不同端口的本地网络地址。 中继终结点有一个由字符串 `sb`、命名空间名称、路径“solver”组成的终结点地址。 这会生成 URI `sb://[serviceNamespace].servicebus.windows.net/solver`，将服务终结点标识为具有完全限定的外部 DNS 名称的服务总线（中继）TCP 终结点。 如果将替换占位符的代码放入`Main`服务**应用程序的**  函数中，则会获得功能服务。 如果希望服务专门侦听中继，请删除本地终结点声明。

### <a name="configure-a-service-host-in-the-appconfig-file"></a>在 App.config 文件中配置服务主机
还可以使用 App.config 文件配置主机。 在此情况下，服务托管代码如以下示例所示。

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

终结点定义将移到 App.config 文件中。 NuGet 包已向 App.config 文件添加一系列定义，这些定义是 Azure 中继必需的配置扩展。 以下示例（与前面的代码完全等效）应该紧靠在 **system.serviceModel** 元素的下面。 此代码示例假设项目 C# 命名空间名命名为“Service”。
将占位符替换为中继命名空间名称和 SAS 密钥。

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

进行这些更改后，该服务将像以前一样启动，但具有两个活动终结点：一个位于本地，一个在云中侦听。

### <a name="create-the-client"></a>创建客户端
#### <a name="configure-a-client-programmatically"></a>以编程方式配置客户端
要使用该服务，可以使用 [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) 对象构造 WCF 客户端。 服务总线使用通过 ACS 实现的基于令牌的安全模型。 [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 类代表具有内置工厂方法的安全令牌提供程序，这些方法可返回一些众所周知的令牌提供程序。 以下示例使用 [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) 方法处理相应 SAS 令牌的获取。 名称和密钥是根据上一部分所述从门户获取的凭据。

首先，在客户端项目中引用服务中的 `IProblemSolver` 约定代码或将其复制到客户端项目中。

然后，替换客户端的 `Main` 方法中的代码，再次将占位符文本替换为中继命名空间和 SAS 密钥。

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

现在可以生成客户端和服务，运行它们（首先运行服务），客户端将调用该服务并输出 **9**。 可以在不同计算机上，甚至跨网络运行客户端和服务器，通信仍将进行。 客户端代码还可以在云中或在本地运行。

#### <a name="configure-a-client-in-the-appconfig-file"></a>在 App.config 文件中配置客户端
以下代码介绍了如何使用 App.config 文件配置客户端。

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

终结点定义将移到 App.config 文件中。 以下示例（与前面列出的代码相同）应紧接在 `<system.serviceModel>` 元素下面。 在此，与之前一样，必须将占位符替换为中继命名空间和 SAS 密钥。

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>后续步骤
现已了解有关 Azure 中继的基础知识，请单击以下链接了解详细信息。

* [什么是 Azure 中继？](relay-what-is-it.md)
* [Azure 服务总线体系结构概述](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* 从 [Azure 示例][Azure samples]下载服务总线示例，或参阅[服务总线示例概述][overview of Service Bus samples]。

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
