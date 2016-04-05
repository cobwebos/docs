<!-- not suitable for Mooncake -->

<properties 
	pageTitle="如何配置 Azure 环境" 
	description="配置、管理和监视 Azure 环境" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="01/04/2016"
	wacn.date=""/>


# 配置 Azure 环境 #

## 概述 ##

Azure 环境是 Azure 中的一项高级层功能，可提供新的缩放性和网络访问功能。通过这个新的缩放功能，你可以将 Azure 的实例置于 VNET 中。此功能可以托管 Web Apps、Mobile Apps 和 API Apps。Logic Apps 目前不能在 ASE 中运行。

如果你不熟悉 Azure 环境 (ASE) 功能，请阅读文档：[什么是 Azure 环境](/documentation/articles/app-service-app-service-environment-intro)。有关如何创建 ASE 的信息，请阅读此处的文档：[如何创建 Azure 环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment)。

从较高层面看，Azure 环境包括以下几个主要组件：

- 在 Azure App 环境托管服务中运行的计算资源
- 存储
- 数据库
- 至少有一个子网的经典“v1”虚拟网络
- Azure App 环境托管服务在其中运行的子网

计算资源用于 4 个资源池。每个 Azure 环境都有一组前端和 3 个辅助角色池。你不需要使用所有 3 个辅助角色池，如果需要，可以只使用其中一个。前端是放置在 ASE 中的应用的 HTTP 终结点。辅助角色是应用实际运行之处。何时需要添加更多前端或更多辅助角色涉及到放在 ASE 中的应用程序以何种方式执行。例如，假设在 ASE 上只有一个应用程序，并且是有大量请求的 hello world 应用程序。在此情况下，你需要增加前端来处理 HTTP 负载，但相对地并不需要增加辅助角色。以手动方式处理这一切是相当复杂，特别是在考虑到每个 ASE 很可能有性能准则互异的不同应用程序运行于其中时。幸运的是，我们已在 Azure 环境中添加了自动缩放功能，这让我们的工作轻松许多。有关 Azure 环境的缩放和自动缩放的详细信息，请单击以下链接：[如何在 Azure 环境中配置自动缩放][ASEAutoscale]

每个 ASE 配置了 500 GB 的存储空间。此空间用于 ASE 中的所有应用。此存储空间属于 ASE 的一部分，目前无法切换为使用客户的存储空间。

数据库保存定义环境及详细说明其中运行的应用程序的信息。这同样也属于 Azure 订阅的一部分，且客户无法直接进行操作。

与 ASE 配合使用的虚拟网络可以是在创建 ASE 时创建的虚拟网络，或者现有的虚拟网络。如果你希望 VNET 所属的资源组不同于用于 ASE 的资源组，则需要在 ASE 创建流程以外单独创建 VNET。同时创建想要使用的子网是个不错的想法，因为在 ASE 创建期间创建子网，强制 ASE 进入与 VNET 相同的资源组。目前仅支持 V1“经典”VNET。

用于管理和监视 Azure 环境的 UI 可从 Azure 管理门户使用。如果你有 ASE，则可能会在边栏上看到 Azure 符号。此符号用于表示 Azure 管理门户中的 Azure 环境。

![][1]

你可以使用图标，或选择边栏底部的 > 形箭号（大于符号），然后选择 Azure 环境。两种方式都可打开 UI，列出所有的 Azure 环境。选择其中一个列出的 ASE 会打开用于监视和管理 ASE 的 UI。

![][2]

这是第一个边栏选项卡，其中显示 ASE 的某些属性和每个资源池的度量值图表。“基本信息”块中显示的某些属性同时也是超链接，将打开与其关联的边栏选项卡。例如，你可以选择“VNET 名称”，以打开与 ASE 运行所在的 VNET 关联的 UI。选择“App Service 计划”和“应用”将分别打开不同的边栏选项卡，其中列出位于 ASE 中的项。

## 监视 ##

图表可让查看每个资源池中各种不同的性能度量值。对于前端池，监视平均 CPU 和内存是很合理的。前端具有分布式负载，只要查看平均值，就可以清楚了解一般性能。但辅助角色池则不同。多个 App Service 计划可以使用同一个辅助角色池中的辅助角色。如果是这样，CPU 和内存使用量将无法提供有用的信息。更重要的是要跟踪已使用和可用的辅助角色数目，特别是在管理此系统以供他人使用时。

所有可在图表中跟踪的度量值也都可以用于设置警报。设置警报的效果与在 Azure Web 应用中的其他位置设置相同。可以从警报 UI 部分设置警报，或钻取到任何度量值 UI 并点击“添加警报”进行设置。
 
![][3]

刚刚所讨论的度量值是 Azure 环境度量值。另外也有可在 App Service 计划级别上使用的度量值。在其中监视 CPU 和内存是很合理的做法。在 ASE 中，所有 ASP 都是专用 ASP。这意味着，在分配给该 ASP 的主机上运行的应用仅限于该 ASP 中的应用。  
若要查看 ASP 的详细信息，请直接从 ASE UI 中的任何清单显示 ASP，或通过浏览 App Service 计划全部列出。

你可能已熟悉 ASE 以外可在 ASP 中使用的自动缩放功能，及其如何利用可用于资源的度量值。Azure 环境自动缩放的工作原理也是如此。你不仅可根据 ASE 中的度量值自动缩放 ASP，也可以设置 ASE 本身的自动缩放规则。有关设置自动缩放的详细信息，请参阅此处的详细指南：[Azure 环境中的自动缩放][ASEAutoscale]


## 属性 ##

“设置”边栏选项卡在显示 ASE 边栏选项卡时自动打开。顶部是“属性”。此处显示了许多与“基本信息”重复的项目，但“VIP 地址”和“出站 IP 地址”是非常有用的项。目前两者可视为同一项，但将来很可能区分出站 IP 地址与 VIP 地址，因此这里列出了两者。因此，如果不计入在 ASE 中设置 SSL 并为单一应用程序添加 IP 地址，在 DNS 中针对在 ASE 中创建的应用程序所设置的 IP，将是在此处的“属性”中所看到的 VIP 地址。

![][4]

## IP 地址 ##

可以在此处将更多 IP 地址添加到 ASE，供应用程序使用。如果在 ASE 中创建想要以 IP SSL 设置的应用程序，需要保留一个 IP 地址仅供该应用程序使用。若要那么做， ASE 必须有一些本身拥有而可分配的 IP 地址。创建 ASE 时，有 1 个地址用于此目的。如果你需要更多地址，请在此处添加。在拖到最大配额之前，请注意如果需要更多，额外的 IP 地址将有其费用。有关增加费用的详细信息列在价格页上：[Azure 定价][AppServicePricing]，请向下滚动到有关“SSL 连接”的部分。额外的价格是 IP SSL 价格。

**注意：**如果你添加更多 IP 地址，请注意这是一项缩放操作。一次只能执行一个缩放操作。共有三种缩放操作：

- 更改 ASE 中可供 IP SSL 使用的 IP 地址数
- 更改用于资源池中的计算资源大小
- 以手动方式或通过自动缩放更改用于资源池中的计算资源数

## 资源池 ##

可以从“设置”进入前端池或辅助角色池 UI。这些资源池边栏选项卡可让查看该资源池的信息，以及提供充分缩放该资源池所需的控制。

每个资源池的基本边栏选项卡分别提供一个图表，其中包含该资源池的度量值。就像 ASE 边栏选项卡中的图表，你可以进入图表并设置所需的警报。从 ASE 边栏选项卡为特定的资源池设置警报，效果等同于从资源池进行设置。在辅助角色池的“设置”边栏选项卡中，你有权列出在此辅助角色池中运行的所有应用或 App Service 计划。

![][5]

### 计算资源数量缩放 ###

若要更清楚地显示在 ASE 中缩放应用程序的情形，请参阅以下指南：[在 Azure 环境中缩放应用](/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment)。如果你想要进一步了解如何配置 ASE 资源池的自动缩放，请先参阅：[Azure 环境中的自动缩放][ASEAutoscale]。其中详细说明了资源池的手动缩放操作。

租户无法直接访问资源池、前端和辅助角色。也就是说，你无法与其建立 RDP、更改其预配或充当其管理员。它们由 Azure 进行操作与维护。不过，计算资源的数量和大小由用户决定。

实际上有三种方式可控制资源池中拥有的服务器数
- 顶部主要 ASE 边栏选项卡中的缩放操作
- 单个资源池的“缩放”边栏选项卡中的手动缩放操作（位于“设置”下）
- 可从单个资源池的“缩放”边栏选项卡设置的自动缩放

若要使用 ASE 边栏选项卡上的缩放操作，请直接单击它，然后将滑块拖到所需的数量并保存。此 UI 还支持更改大小。

![][6]

若要使用特定资源池中的手动或自动缩放功能，请先进入 ASE 边栏选项卡，并转到“设置”。在“设置”上依需求打开前端池或或辅助角色池，然后打开想要更改的池。“设置”下面有一个“缩放”箭头符号。它所打开的边栏选项卡可以用于手动缩放或自动缩放。

![][7]

可对 Azure 环境进行配置以使用最多 55 个总计算资源。在这 55 个计算资源中，只有 50 个可用于承载工作负荷。原因在于两个方面。至少有 2 个前端计算资源。还剩最多 53 个来支持辅助池分配。为了提供容错功能，还需根据以下规则分配额外计算资源：

- 每个辅助角色池至少需要一个额外计算资源，该资源不能是已分配的工作负荷
- 当辅助角色池中计算资源的数量超出特定值时，则需要另一个计算资源以实现容错。在前端池中不需要这样做。

在任何单个辅助池中，对于分配到辅助池的给定 X 个资源，容错要求如下：

- 如果 X 介于 2 到 20，则可用于工作负荷的可用计算资源量为 X-1
- 如果 X 介于 21 到 40，则可用于工作负荷的可用计算资源量为 X-2
- 如果 X 介于 41 到 53，则可用于工作负荷的可用计算资源量为 X-3

请记住，最少的使用量具有 2 个前端服务器和 2 个辅助角色。上述说明可通过几个示例来澄清。

- 如果在单个池中有 30 个辅助角色，则其中有 28 个可用于托管工作负荷。 
- 如果在单个池中有 2 个辅助角色，则有 1 个可用于托管工作负荷。
- 如果在单个池中有 20 个辅助角色，则有 19 个可用于托管工作负荷。  
- 如果在单个池中有 21 个辅助角色，则同样只有 19 个可用于托管工作负荷。  

容错是很重要的环节，但在缩放超过特定阈值时必须留意。如果想要从 20 个实例添加更多容量，请缩放到 22 或更高，因为 21 并不额外添加任何容量。缩放为 40 以上时也是一样，下一个添加容量的数为 42。

### 计算资源大小缩放 ###

除了能够管理可分配到给定池的计算资源的数量之外，你还可控制其大小。在 Azure 环境中，可从 4 种不同大小（标记为 P1 到 P4）进行选择。有关这些大小及其定价的详细信息，请参阅此处：[Azure 定价](/documentation/services/web-sites) P1 到 P3 计算资源的大小与一般可用大小相同。P4 计算资源提供具有 14 GB RAM 的 8 个内核，仅在 Azure 环境中可用。

如果想要更改池中所使用的计算资源大小，可通过两种方式来实现。有缩放命令可从 ASE边栏选项卡使用，也可从“定价层”边栏选项卡使用（移到单个资源池中的“设置”即可进入）。

![][8]

在进行任何更改之前，特别要注意一些事项：

- 所做的更改可能需要几小时才能完成，具体取决于更改的大小
- 存在正在进行的 Azure 环境配置更改时，无法启动另一更改
- 如果更改辅助角色池中所用计算资源的大小，可能会导致在该辅助角色池中运行的应用运行中断

将其他实例添加到辅助角色池是一项良性操作，不会导致任何应用程序因为该池中的资源而中断。更改辅助池中所用计算资源的大小则不然。为了避免在对辅助角色池进行大小更改期间出现任何应用停机时间，最好执行以下操作：

- 使用未使用的辅助池以所需大小显示所需实例
- 将 App Service 计划缩放到新的辅助池。  
 
与使用运行中工作负荷更改计算资源大小相比，这对运行应用造成的干扰较小。有关在 Azure 环境中缩放应用的详细信息，请转到此处：[在 Azure 环境中缩放应用](/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment)

## 虚拟网络 ##

不同于包含 ASE 的托管服务，[虚拟网络][virtualnetwork]和子网都在用户的控制之下。Azure 环境的确有一些网络要求，但其余部分是由用户控制。这些 ASE 要求包括：

- 经典“v1”VNET 
- 至少有 8 个地址的子网 
- 虚拟网络必须为区域虚拟网络  
 
可以通过虚拟网络 UI 或 Powershell 管理 VNET。

因为此功能将 Azure 置于 VNET 中，这意味着托管在 ASE 中的应用现在可直接访问通过 ExpressRoute 或站点到站点 VPN 提供的资源。Azure 环境中的应用不需要其他网络功能即可访问托管 Azure 环境的 VNET 可用的资源。这意味着你无需使用 VNET 集成或混合连接，即可将资源移入或连接到 VNET。但你仍可以使用这两项功能访问网络中未连接到 VNET 的资源。例如，你可以使用 VNET 集成来与订阅中未连接到 ASE 所在的 VNET 的 VNET 相集成。像平时一样，你仍可使用混合连接来访问其他网络中的资源。

如果 VNET 配置了 ExpressRoute VPN，则你应注意 ASE 的某些路由要求。某些用户定义的路由 (UDR) 配置与 ASE 不兼容。有关在包含 ExpressRoute 的 VNET 中运行 ASE 的详细信息，请参阅以下文档：[在包含 ExpressRoute 的 VNET 中运行 Azure 环境][ExpressRoute]

现在你还可以使用网络安全组来控制对应用的访问。通过此功能，可以将 Azure 环境锁定到你想限定使用的 IP 地址。有关如何操作的详细信息，请参阅此处的文档：[如何在 Azure 环境中控制入站流量](/documentation/articles/app-service-app-service-environment-control-inbound-traffic)。

## 删除 Azure 环境 ##

如果想要删除 Azure 环境，使用“Azure 环境”边栏选项卡顶部的“删除”操作即可。当你执行此操作时，系统将提示你输入 Azure 环境的名称，以确认你确实想要执行此操作。注意：删除 Azure 环境时，将同时删除其中包含的所有内容。

![][9]

## 入门

若要开始使用 Azure 环境，请参阅[如何创建 Azure 环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment)

有关 Azure 平台的详细信息，请参阅 [Azure Web 应用](/documentation/services/web-sites)。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: /documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: /documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: /documentation/articles/virtual-networks-faq/
[AppServicePricing]: /home/features/web-site/#price
[AzureAppService]: /documentation/services/web-sites/
[ASEAutoscale]: /documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: /documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=Mooncake_0328_2016-->