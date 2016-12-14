---
title: "在 Azure 云中使用负载平衡服务 | Microsoft 文档"
description: "本教程介绍了如何使用 Azure 负载平衡项目组合（流量管理器、应用程序网关和负载平衡器）创建方案"
services: traffic-manager
documentationcenter: 
author: liumichelle
manager: vitinnan
editor: 
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 90918f6bb23f0f4e3c009506e1b12c028c45f02b

---

# <a name="using-load-balancing-services-in-the-azure-cloud"></a>在 Azure 云中使用负载平衡服务

## <a name="introduction"></a>介绍

Microsoft Azure 提供了多种服务，以便你可以管理网络流量的分布和负载平衡方式。 你可以单独使用这些服务，也可以根据具体需要组合其方法，从而生成最佳解决方案。

在本教程中，我们将先定义一个客户用例，并观察如何通过下列 Azure 负载平衡项目组合来提升用例的可靠性和性能：流量管理器、应用程序网关和负载平衡器。 然后，我们将分步说明如何创建向 VM 分布流量且允许管理不同类型请求的异地冗余部署。

从概念上来讲，每个服务都在负载平衡层次结构中起到不同的作用。

1. **流量管理器**提供全局 DNS 负载平衡。  它会查找传入 DNS 请求，然后按照客户选择的路由策略响应正常运行的终结点。 路由方法包括：性能路由，根据延迟将请求者发送到最近的终结点；优先级路由，将所有流量定向到终结点，并将其他终结点作为备份；加权轮循机制路由，根据分配到每个终结点的权重分布流量。 客户端直接连接相应的终结点。 Azure 流量管理器会检测终结点何时运行不正常，并将客户端重定向到另一个正常运行的实例。 若要详细了解此服务，请参阅 [Azure 流量管理器文档](traffic-manager-overview.md)。
2. **应用程序网关**以服务形式提供应用程序传送控制器 (ADC)，借此为应用程序提供各种第 7 层负载平衡功能。 它使客户能够通过将 CPU 密集型 SSL 终端的负载卸载到应用程序网关上来优化 Web 场生产率。 其他第 7 层路由功能包括传入流量的轮循机制分布、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后托管多个网站的功能。 可以将应用程序网关配置为面向 Internet 的网关、仅内部网关或这两者的组合。 应用程序网关完全由 Azure 托管，可缩放且高度可用。 它提供一组丰富的诊断和日志记录功能，以便更好地进行管理。
3. **负载平衡器**是 Azure SDN 堆栈的一部分， 为所有 UDP 和 TCP 协议提供高性能、低延迟的第 4 层负载平衡服务。  它管理入站和出站连接。  你不仅可以配置公共和内部负载平衡的终结点，还可以定义规则来使用 TCP 和 HTTP 运行状况探测选项将入站连接映射到后端池目标，从而管理服务可用性。

## <a name="scenario"></a>方案

在此示例方案中，我们使用的是提供两种类型内容（图像和动态呈现的网页）的简单网站。 此网站需要进行异地冗余部署，应该从最近（延迟最低）的位置为用户提供服务。 应用程序开发者决定，为访问与 /images/* 模式匹配的任意 URL 的用户，从不同于 Web 场其余部分的 VM 专用池提供服务。

另外，提供动态内容的默认 VM 池需要与高可用性群集上托管的后端数据库进行通信。 整个部署通过 Azure Resource Manager 进行预配。

利用流量管理器、应用程序网关和负载平衡器，让此网站实现下面的设计目标：

1. **多异地冗余**：通过使用流量管理器，如果一个区域出现故障，流量会顺畅路由到下一个最佳区域，不受应用程序所有者的任何干预。
2. **降低延迟**：由于 Azure 流量管理器自动将客户定向到最近区域，因此请求获取网页内容时的延迟较低。
3. **独立可缩放**：通过根据内容类型区分 Web 应用程序工作负荷，应用程序所有者能够缩放各请求工作负荷（互不影响）。 应用程序网关确保根据指定的规则和应用程序的运行状况将流量路由到正确的池。
4. **内部负载平衡**：在高可用性群集前面放置负载平衡器，仅对应用程序相应公开数据库的正常运行的可用终结点。  此外，数据库管理员还可以跨群集分布主动和被动副本（与前端应用程序互不影响），从而优化工作负荷。  负载平衡器提供与高可用性群集的连接，并确保只有正常运行的数据库才会收到连接请求。

下图展示了此方案的体系结构：

![方案示意图](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> 此示例只是 Azure 提供的负载平衡服务的多种可能配置中的一种。 可以最符合你的负载平衡需求的方式，混合搭配 Azure 流量管理器、应用程序网关和负载平衡器。 例如，如果 SSL 卸载或第 7 层处理没有必要，负载平衡器可以替代应用程序网关。

## <a name="setting-up-the-load-balancing-stack"></a>设置负载平衡堆栈

### <a name="step-1-create-a-traffic-manager-profile"></a>第 1 步：创建流量管理器配置文件

1. 转到 Azure 门户，单击“**新建**”，然后在应用商店中搜索“流量管理器配置文件”
2. 在“创建流量管理器配置文件”边栏选项卡中，填写用于创建流量管理器配置文件的基本信息：

   * **名称** - 为流量管理器配置文件指定 DNS 前缀名称
   * **路由方法** - 选择流量路由方法策略。有关方法的详细信息，请参阅[关于流量管理器流量路由方法](traffic-manager-routing-methods.md)
   * **订阅** - 包含配置文件的订阅
   * **资源组** - 包含流量管理器配置文件的资源组，可以是新建的资源组，也可以是现有资源组
   * **资源组位置** - 虽然流量管理器是全局服务，不局限于某个位置，但必须为与流量管理器配置文件相关联的元数据驻留所在组指定区域。 此位置对配置文件的运行时可用性没有任何影响。

3. 单击“**创建**”，生成流量管理器配置文件

    ![“创建流量管理器”边栏选项卡](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>第 2 步：创建应用程序网关

1. 转到 Azure 门户，在左侧菜单中，依次单击“**新建**” > “**网络**” > “**应用程序网关**”
2. 下一步，填写有关应用程序网关的基本信息。 完成后，单击“确定”。 基本设置需要的信息如下：

   * **名称** - 应用程序网关的名称。
   * **SKU 大小** - 此设置是指应用程序网关的大小，可用选项包括小型、中型和大型。
   * **实例计数** - 实例的数目，此值应该是 2 到 10 之间的数字。
   * **资源组** - 用于保存应用程序网关的资源组，可以是现有资源组，也可以是新的资源组。
   * **位置** - 应用程序网关所在的区域，与资源组的位置相同。 位置很重要，因为虚拟网络和公共 IP 必须与网关位于同一位置。

3. 接下来，定义应用程序网关的虚拟网络、子网、前端 IP 和侦听器配置。 在此方案中，前端 IP 地址是**公共**的，以便稍后可将其作为终结点添加到流量管理器配置文件中。
4. 下一个要配置的应用程序网关设置是侦听器配置。 如果使用的是 http，无需进行任何配置，只需单击“**确定**”。 如果使用的是 https，需要进行其他配置，请参阅[创建应用程序网关](../application-gateway/application-gateway-create-gateway-portal.md)（从第 9 步开始）。

#### <a name="configure-url-routing-for-application-gateways"></a>配置应用程序网关的 URL 路由

选择后端池时，使用基于路径的规则配置的应用程序网关除了采用轮循机制分布外，还采用请求 URL 路径模式。 在此方案中，我们要添加基于路径的规则，将包含“/images/\*”的任意 URL 定向到映像服务器池。 若要详细了解如何为应用程序网关配置基于 URL 路径的路由，请参阅[为应用程序网关创建基于路径的规则](../application-gateway/application-gateway-create-url-route-portal.md)。

![Web 层示意图](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. 从资源组转到在上述步骤中创建的应用程序网关实例。
2. 在“设置”下，依次选择“**后端池**”和“添加”，添加要与 Web 层后端池相关联的 VM。
3. 在“添加后端池”边栏选项卡中，输入后端池的名称，以及池中驻留的计算机的所有 IP 地址。 在此方案中，我们要连接虚拟机的两个后端服务器池。

    ![应用程序网关的“添加后端池”边栏选项卡](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. 接下来，在应用程序网关的“设置”下，选择“**规则**”，然后单击“**基于路径**”按钮，添加新规则。

    ![应用程序网关的“添加路径规则”边栏选项卡](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. 在“添加基于路径的规则”边栏选项卡中，输入以下信息来配置规则：

   * 基本设置：
     + **名称** - 可在门户中访问的规则的易记名称
     + **侦听器** - 对规则使用的侦听器
     + **默认后端池** - 要用于默认规则的后端池
     + **默认 HTTP 设置** - 要用于默认规则的 HTTP 设置
   * 基于路径的规则：
     + **名称** - 基于路径的规则的易记名称
     + **路径** - 用于转发流量的路径规则。
     + **后端池** - 要与此规则配合使用的后端池
     + **Http 设置** - 要与此规则配合使用的 HTTP 设置

     > [!IMPORTANT]
     > 路径：有效路径必须以“/”开头。 只允许在末尾使用通配符“\*”。 有效示例包括 /xyz、/xyz\* 或 /xyz/\*

     ![应用程序网关的“添加路径规则”边栏选项卡](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>第 3 步：将应用程序网关添加到流量管理器终结点

在此方案中，流量管理器连接在不同区域驻留的应用程序网关实例（如上述步骤中所配置）。 现在，应用程序网关已配置，下一步是连接应用程序网关与我们的流量管理器配置文件。

1. 转到流量管理器配置文件实例（为此，可以在资源组中查找，也可以在“所有资源”中搜索流量管理器配置文件的名称）。
2. 在此边栏选项卡中，依次选择“**终结点**”和“**添加**”，添加新的终结点。

    ![流量管理器的“添加终结点”边栏选项卡](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. 在“添加终结点”边栏选项卡中，填写用于新建终结点的信息

   * **类型** - 要执行负载平衡的终结点的类型。在此方案中，为 Azure 终结点，这是因为我们要将其连接到上文中配置的应用程序网关实例
   * **名称** - 端终结点的名称
   * **目标资源类型** - 选择“公共 IP 地址”，然后在下面的“目标资源”设置中，选择上文中配置的应用程序网关的公共 IP

     ![流量管理器的“添加终结点”边栏选项卡](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. 此时，你可以测试设置，具体方法为使用流量管理器配置文件的 DNS（在此示例中，为 TrafficManagerScenario.trafficmanager.net）进行访问。 你可以重新发送请求、打开/关闭不同区域中创建的 VM/Web 服务器，并能更改流量管理器配置文件设置来测试设置。

### <a name="step-4-create-the-load-balancer"></a>第 4 步：创建负载平衡器

在此方案中，负载平衡器将 Web 层中的连接分发到高可用性群集中的数据库。

如果高可用性数据库群集使用的是 SQL AlwaysOn，请参阅[配置一个或多个 Always On 可用性组侦听器](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，了解分步说明。

若要详细了解如何配置内部负载平衡器，请参阅[在 Azure 门户中创建内部负载平衡器](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)

1. 转到 Azure 门户，在左侧菜单中，依次单击“**新建**” > “**网络**” > “**负载平衡器**”
2. 在“创建负载平衡器”边栏选项卡中，为负载平衡器选择一个名称
3. 将“**类型**”设置为“内部”，然后选择相应的虚拟网络和子网以供负载平衡器驻留
4. 在“**IP 地址分配**”下，选择“动态”或“静态”
5. 接下来，在“**资源组**”下，选择负载平衡器的资源组
6. 在“**位置**”下，选择负载平衡器的相应区域
7. 最后，单击“**创建**”，生成负载平衡器

#### <a name="connect-backend-database-tier-to-load-balancer"></a>连接后端数据库层和负载平衡器

1. 在资源组中，找到在上述步骤中创建的负载平衡器。
2. 在“设置”下，依次单击“**后端池**”和“**添加**”，添加新的后端池
3. 在“添加后端池”边栏选项卡中，输入后端池的名称
4. 此时，你可以向后端池单独添加计算机，也可以向后端池添加可用性集。

   ![负载平衡器的“添加后端池”边栏选项卡](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

#### <a name="configure-a-probe"></a>配置探测

1. 在负载平衡器的“设置”下，依次选择“**探测**”和“**添加**”，添加新的探测
2. 在“添加探测”边栏选项卡中，输入探测的“**名称**”
3. 为探测选择“**协议**”。 对于数据库，不妨添加 TCP 探测，而不是 HTTP 探测。   若要详细了解负载平衡器探测，请参阅[了解负载平衡器探测](../load-balancer/load-balancer-custom-probe-overview.md)。
4. 接下来，输入要在访问探测时利用的数据库的“**端口**”。
5. 在“**时间间隔**”下，指定多久探测一次应用程序
6. 在“**不正常阈值**”下，指定必须连续发生多少次探测故障才能将后端 VM 视为不正常运行。
7. 单击“**确定**”，创建探测

   ![负载平衡器探测](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

#### <a name="configure-load-balancing-rules"></a>配置负载平衡规则

1. 在负载平衡器的“设置”下，依次选择“**负载平衡规则**”和“**添加**”，创建规则
2. 在“添加负载平衡规则”边栏选项卡中，输入负载平衡规则的“**名称**”
3. 选择“**负载平衡器的前端 IP 地址**”、“**协议**”和“**端口**”
4. 在“**后端端口**”下，指定要在后端池中使用的端口
5. 选择在上述步骤中创建的“**后端池**”和“**探测**”，对其应用规则
6. 在“**会话持久性**”下，选择要采用的会话保留方式
7. 在“**空闲超时**”下，指定多少分钟算空闲超时
8. 对于“**浮动 IP**”，选择“禁用”或“启用”
9. 单击“**确定**”，创建规则

### <a name="step-5-connect-web-tier-vms-to-load-balancer"></a>第 5 步：连接 Web 层 VM 与负载平衡器

现在，我们要在 Web 层 VM 上运行的应用程序中配置 IP 地址和负载平衡器前端端口，以进行任意数据库连接。 此配置专门针对在这些 VM 上运行的应用程序。 若要配置目标 IP 地址和端口，请参阅应用程序文档，了解详细信息。 若要查找前端 IP 地址，请转到 Azure 门户中“负载平衡器”设置边栏选项卡上的“前端 IP 池”。

![负载平衡器的“前端 IP 池”边栏选项卡](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>后续步骤

* [流量管理器概述](traffic-manager-overview.md)
* [应用程序网关概述](../application-gateway/application-gateway-introduction.md)
* [Azure Load Balancer 概述](../load-balancer/load-balancer-overview.md)



<!--HONumber=Nov16_HO3-->


