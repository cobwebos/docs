# Service Bus 的 AMQP 1.0 支持


>Azure Service Bus 和 Service Bus for Windows Server (Service Bus 1.1） 支持高级消息队列协议 (AMQP) 1.0。AMQP 使您能够构建跨平台的混合应用程序使用一种开放标准协议。您可以构造使用使用不同的语言和框架，并在不同的操作系统上运行构建的组件的应用程序。高效且完全无损，所有这些组件可以连接到 Service Bus 和无缝交换结构化的业务消息。

## 简介：什么是 AMQP 1.0 以及为何重要？

一直以来，面向消息的中间件产品始终使用客户端应用程序和代理之间的通信的专用协议。这意味着一旦选择了特定供应商的消息传递代理，您必须使用该供应商的库来连接到该代理的客户端应用程序。这会导致在一定程度"锁 in"该供应商，因为应用程序移植到不同的产品需要重新编写的所有连接的应用程序代码。 

此外，连接来自不同供应商的消息传递代理比较棘手并且通常需要通过应用程序级桥接，若要将消息从一个系统移到另一个，并在其专用消息格式之间转换。这是常见的要求 ；例如，当提供新的统一到较旧的不同系统或合并后集成 IT 系统的接口。

软件产业是飞速发展的产业 ；新的编程语言和应用程序框架的创作速度有时会非常惊人。同样，IT 系统的要求随着时间的推移不断发展，并且开发人员想要充分利用最新的语言和框架。但是，有时所选的消息传递供应商不支持这些平台。由于协议是专有的因此不能供其他人为这些新平台提供库。因此，您是只能构建网关、 网桥和其他方案。

AMQP （高级消息队列协议） 的开发 1.0 推动了这些问题。这种协议源于 JP Morgan Chase，像多数金融服务公司，都需要较大的面向消息的中间件。公司目标很简单： 若要创建一个开放标准消息传递协议，使得它可以构建基于消息的应用程序使用不同的语言、 框架和操作系统中，使用构建的组件，所有使用不同的供应商的同类最佳组件。

## AMQP 1.0 技术特性

AMQP 1.0 是一个高效、可靠的线级消息传递协议，可用于构建强大、跨平台的消息传递应用程序。协议有一个简单的目标： 若要定义安全、 可靠且高效传输消息的两个参与方之间的例行过程。消息本身使用支持不同发送者和接收者完全无损的结构化的业务消息交换的可移植数据表示形式进行编码。以下是最重要的功能的摘要：

*    **高效**：AMQP 1.0 是一种面向连接的协议，通过它将二进制编码的协议的说明进行操作和业务消息传输。它融合了复杂的流控制方案，可最大限度地利用网络和连接的组件。也就是说，该协议旨在效率、 灵活性和互操作性之间取得平衡。
*    **可靠**：使用 AMQP 1.0 协议允许与一系列可靠性保证，从发即弃到可靠，完全交换消息的一次确认传送。
*    **灵活**：AMQP 1.0 是一种灵活的协议，可用于支持不同的拓扑。可以对客户端到客户端、 客户端到代理以及代理到代理通信使用相同的协议。
*    **Broker 模型独立于**：AMQP 1.0 规范不会在由代理所使用的消息传递模型上的任何要求。这意味着它是可以轻松地将 AMQP 1.0 支持添加到现有消息传递代理。

## AMQP 1.0 是一种标准 （带有大写字母的 ')

AMQP 1.0 一直在自由 20 多家公司的核心小组的 2008 年以来的开发技术提供商和最终用户企业） 组成。在此期间，用户企业提供其实际业务需求和技术供应商则开发该协议来满足这些要求。在整个过程中，供应商参加了研讨会，并在会上协作验证其实现之间的互操作性。

2011 年 10 月，过渡到进步的结构化信息标准 (OASIS) 和 OASIS AMQP 1.0 标准的组织内的技术委员会的开发工作已于 2012 年 10 月发布。以下的公司在开发该标准期间参与了技术委员会：

*    **技术供应商**：Axway Software、 Huawei 技术，IIT 软件，INETCO Systems、 Kaazing，Microsoft，Mitre Corporation、 Primeton 技术，进度软件，红色 Hat、 SITA、 Software AG、 Solace Systems、 VMware、 WSO2、 Zenika。
*    **用户企业**：Bank of America、 Credit Suisse、 Deutsche Boerse、 Goldman Sachs、 JPMorgan Chase。

一些种开放标准的公认好处包括：

*    供应商锁定的几率较低
*    互操作性
*    有大量库和工具可供使用
*    不会过时
*    随时可找到知识渊博的工作人员
*    风险较低且可控

## AMQP 1.0 和 Service Bus

添加 AMQP 1.0 意味着您现在可以利用队列和发布/订阅的 Service Bus 中转消息传递功能从一系列使用有效的二进制协议的平台。此外，您可以构建应用程序结合使用多个语言、 框架和操作系统构建的组件组成。

下图所示的示例部署，在其中 Java 客户端运行在 Linux 上，使用标准 Java 消息服务 (JMS) api，API 和.NET 客户端运行在 Windows 上，写入交换通过 Service Bus 使用 AMQP 1.0 的消息。

![][0]

**图 1：演示使用 Service Bus 和 AMQP 1.0 进行跨平台消息传送的部署方案示例**

在这种情况下，已知下列客户端库将使用 Service Bus：

<table>
  <tr>
    <th>语言</th>
    <th>库</th>
  </tr>
  <tr>
    <td>Java</td>
    <td>Apache Qpid Java 消息服务 (JMS) 客户端<br/>
        软件 SwiftMQ IIT Java 客户端</td>
  </tr>
  <tr>
    <td>C</td>
    <td>Apache Qpid Proton-C</td>
  </tr>
  <tr>
    <td>PHP</td>
    <td>Apache Qpid Proton-PHP</td>
  </tr>
  <tr>
    <td>Python</td>
    <td>Apache Qpid Proton-Python</td>
  </tr>

</table>


**图 2：AMQP 1.0 客户端库表**

有关如何获取和使用 Service Bus 中使用这些库的详细信息，请参阅 Service Bus AMQP 开发人员指南。请参阅下面的"参考资料"部分将详细信息。

## 摘要

*    AMQP 1.0 是一个开放、可靠的消息传递协议，可用于构建跨平台的混合应用程序。AMQP 1.0 是一种 OASIS 标准。
*    AMQP 1.0 支持现已推出 Azure Service Bus 和 Service Bus for Windows Server (Service Bus 1.1） 中。定价是与现有协议相同的。

## 参考

*    [如何将 AMQP 1.0 用于 Service Bus.NET API](http://aka.ms/lym3vk)
*    [如何使用 Service Bus 和 AMQP 1.0 的 Java 消息服务 (JMS) API](http://aka.ms/ll1fm3)
*    [Service Bus AMQP 1.0 开发人员指南](http://msdn.microsoft.com/zh-cn/library/windowsazure/jj841071.aspx)
*    [OASIS 高级消息队列协议 (AMQP) 1.0 版规范](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)

[0]: ./media/service-bus-amqp-overview/Example1.png

