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
	ms.date="06/20/2016"
	wacn.date=""/>


# 配置 Azure 环境 #

## 概述 ##

从较高层面看，Azure 环境包括以下几个主要组件：

- 在 Azure App 环境托管服务中运行的计算资源
- 存储
- 数据库
- 至少有一个子网的经典“v1”虚拟网络
- Azure App 环境托管服务在其中运行的子网

### 计算资源

计算资源用于 4 个资源池。每个 Azure 环境都有一组前端和 3 个可能的辅助角色池。你不需要使用所有 3 个辅助角色池，如果需要，可以只使用其中一个或两个。租户无法直接访问资源池、前端和辅助角色。你无法与其建立 RDP、更改其预配或充当其管理员，但可以查看其数量和大小。在 ASE 中，有 4 个大小选项，标记为 P1 到 P4。有关这些大小及其定价的详细信息，请参阅此处的 [Azure 定价](/documentation/services/web-sites)。
一次只能执行一项缩放操作。

**前端**：前端是保留在 ASE 中的应用的 HTTP/HTTPS 终结点。不能在前端中运行工作负荷。

- ASE 以 2 个 P2 开始，这对于开发/测试工作负荷和低级生产工作负荷而言已经足够。强烈建议针对中型到重型生产工作负荷使用 P3。
- 对于中型到重型生产工作过程而言，建议至少有 4 个 P3 以确保在计划的维护开始执行时有足够的前端在运行。计划的维护活动将一次关闭 1 个前端，因此在维护活动执行期间会降低总体可用的前端容量。
- 无法立即添加新的前端实例。它们可能需要 2-3 小时来预配。
- 若要进一步微调，客户应监视前端池的 CPU 百分比、内存百分比，以及活动请求指标。如果运行 P3 时 CPU 或内存百分比超过 70%，则应添加更多前端。如果每个前端的活动请求数目平均达到 15K 到 20K 个请求，也应该添加更多前端。总体目标是在运行 P3 时，将 CPU 和内存百分比维持在低于 70%，并将每个前端的活动请求平均数维持在低于 15K 个请求。  

**辅助角色**：辅助角色是应用实际运行所在的位置。当你提高 App Service 计划时，将用尽关联辅助角色池中的辅助角色。

- 无法立即添加辅助角色。无论添加多少辅助角色，都可能需要 2 到 3 小时来预配。
- 缩放任何池的计算资源大小时，每个更新域都将需要 2-3 小时来完成缩放。ASE 中有 20 个更新域。如果缩放包含 10 个实例的辅助角色池的计算大小，可能需要 20 到 30 个小时才能完成。 
- 如果更改辅助角色池中所用计算资源的大小，将会导致在该辅助角色池中运行的应用冷启动

更改未运行任何应用的辅助角色池的计算资源大小的最快捷方式是：

- 将实例计数缩减为 0。这需要大约 30 分钟来解除分配实例
- 选择新的计算大小与实例数目。从此处开始，需要 2 到 3 小时来完成。

如果应用程序需要更大的计算资源大小，你将无法利用前面的指导。你可以改为在另一个辅助角色池中填充具有所需大小的辅助角色，并将应用转移到该池，而无需更改托管那些应用的辅助角色池的大小。

- 在另一个辅助角色池中创建具有所需计算大小的其他实例。这将需要 2 到 3 小时来完成。
- 将需要更大大小的、托管应用程序的 App Service 计划重新分配到新配置的辅助角色池。这是一个快速操作，在一分钟内应可完成。  
- 如果不再需要那些未使用的实例，请缩减第一个辅助角色池。此操作大约需要 30 分钟来完成。

**自动缩放**：可帮助管理计算资源消耗的工具之一就是可以针对前端或辅助角色池执行的自动缩放。例如，你可以在上午增加任何池类型的实例数，然后在傍晚减少实例数，或在辅助角色池中可用的辅助角色数降到低于特定阈值时，增加实例数。如果你想要设置有关计算资源池指标的自动缩放规则，请记得考虑预配所需的时间。有关自动缩放 Azure 环境的更多详细信息，请转到[如何在 Azure 环境中配置自动缩放][ASEAutoscale]

### 存储

每个 ASE 配置了 500 GB 的存储空间。此空间用于 ASE 中的所有应用。此存储空间属于 ASE 的一部分，目前无法切换为使用客户的存储空间。如果调整 VNET 路由或安全性，你仍然需要允许访问 Azure 存储空间，否则 ASE 将无法正常工作。

### 数据库

数据库保存定义环境及详细说明其中运行的应用程序的信息。这同样也属于 Azure 订阅的一部分，且客户无法直接进行操作。如果调整 VNET 路由或安全性，你仍然需要允许访问 SQL Azure，否则 ASE 将无法正常工作。

### 网络

与 ASE 配合使用的虚拟网络可以是在创建 ASE 时创建的虚拟网络，或者现有的虚拟网络。如果你希望 VNET 所属的资源组不同于用于 ASE 的资源组，则需要在 ASE 创建流程以外单独创建 VNET。如果在创建 ASE 创建创建子网，将会强制 ASE 进入与 VNET 相同的资源组。  

用于 ASE 的 VNET 存在一些限制：

- 目前仅支持 V1“经典”VNET
- VNET 必须为区域 VNET
- 利用 2016 年 6 月的最新更改，现在可以将 ASE 部署到使用公用地址范围或 RFC1918 地址空间（即专用地址）的虚拟网络。若要使用具有公用地址范围的虚拟网络，你需要提前创建子网，然后在 ASE 创建 UX 中选择该子网。
- 需要有包含 8 个或更多个地址且为 ASE 部署位置的子网
- 将某个子网用于托管 ASE 之后，无法更改该子网的地址范围。因此，建议子网至少包含 64 个地址以适应将来的 ASE 增长 
- **用于托管 ASE 的子网不得包含任何其他计算资源。**

不同于包含 ASE 的托管服务，[虚拟网络][virtualnetwork]和子网都在用户的控制之下。可以通过虚拟网络 UI 或 Powershell 管理 VNET。

因为此功能将 Azure 置于 VNET 中，这意味着托管在 ASE 中的应用现在可直接访问通过 ExpressRoute 或站点到站点 VPN 提供的资源。Azure 环境中的应用不需要其他网络功能即可访问托管 Azure 环境的 VNET 可用的资源。这意味着你无需使用 VNET 集成或混合连接，即可将资源移入或连接到 VNET。但你仍可以使用这两项功能访问网络中未连接到 VNET 的资源。例如，你可以使用 VNET 集成来与订阅中未连接到 ASE 所在的 VNET 的 VNET 相集成。像平时一样，你仍可使用混合连接来访问其他网络中的资源。

如果 VNET 配置了 ExpressRoute VPN，则你应注意 ASE 的某些路由要求。某些用户定义的路由 (UDR) 配置与 ASE 不兼容。有关在包含 ExpressRoute 的 VNET 中运行 ASE 的更多详细信息，请参阅以下文档：[在包含 ExpressRoute 的 VNET 中运行 Azure 环境][ExpressRoute]

现在你还可以使用网络安全组来控制对应用的访问。通过此功能，可以将 Azure 环境锁定到你想限定使用的 IP 地址。有关如何操作的详细信息，请参阅以下文档：[如何在 Azure 环境中控制入站流量](/documentation/articles/app-service-app-service-environment-control-inbound-traffic)。

## 门户

用于管理和监视 Azure 环境的 UI 可从 Azure 门户使用。如果你有 ASE，则可能会在边栏上看到 Azure 符号。此符号用于表示 Azure 门户中的 Azure 环境。

![][1]

你可以使用图标，或选择边栏底部的 > 形箭号（大于符号），然后选择 Azure 环境。两种方式都可打开 UI，列出所有的 Azure 环境。选择其中一个列出的 ASE 会打开用于监视和管理 ASE 的 UI。

![][2]

这是第一个边栏选项卡，其中显示 ASE 的某些属性和每个资源池的度量值图表。“基本信息”块中显示的某些属性同时也是超链接，将打开与其关联的边栏选项卡。例如，你可以选择“VNET 名称”，以打开与 ASE 运行所在的 VNET 关联的 UI。选择“App Service 计划”和“应用”将分别打开不同的边栏选项卡，其中列出位于 ASE 中的项。

### 监视

图表可让查看每个资源池中各种不同的性能度量值。对于前端池，可以监视平均 CPU 和内存。对于辅助角色池，可以监视已用数量和可用数量。多个 App Service 计划可以使用同一个辅助角色池中的辅助角色。工作负荷不是通过与前端服务器相同的方式分布，因此 CPU 和内存使用率不能提供很多有用的信息。更重要的是要跟踪已使用和可用的辅助角色数目，特别是在管理此系统以供他人使用时。

所有可在图表中跟踪的度量值也都可以用于设置警报。设置警报的效果与在 Azure Web 应用中的其他位置设置相同。可以从警报 UI 部分设置警报，或钻取到任何度量值 UI 并点击“添加警报”进行设置。
 
![][3]

刚刚所讨论的度量值是 Azure 环境度量值。另外也有可在 App Service 计划级别上使用的度量值。在其中监视 CPU 和内存是很合理的做法。在 ASE 中，所有 ASP 都是专用 ASP。这意味着，在分配给该 ASP 的主机上运行的应用仅限于该 ASP 中的应用。  
若要查看 ASP 的详细信息，请直接从 ASE UI 中的任何清单显示 ASP，或通过浏览 App Service 计划全部列出。

### 设置

ASE 边栏选项卡中有一个包含几项重要功能的“设置”部分

**设置 > 属性**：“设置”边栏选项卡在显示 ASE 边栏选项卡时自动打开。顶部是“属性”。此处显示了许多与“基本信息”重复的项目，但“VIP 地址”和“出站 IP 地址”是非常有用的项。

![][4]

**设置 > IP 地址**：当你在 ASE 中创建 IP SSL 应用时，需要有一个 IP SSL 地址。为此，你的 ASE 需要拥有一些可分配的 IP SSL 地址。创建的 ASE 最初有 1 个 IP SSL 用于此目的，但你可以添加更多地址。附加 IP SSL 地址需要付费，如 [Azure 定价][AppServicePricing]中有关 SSL 连接的部分所述。额外的价格是 IP SSL 价格。

**设置 > 前端池/辅助角色池**：其中的每个资源池边栏选项卡可让你查看有关该资源池的信息，并获得全面缩放该资源池所需的控制权。

每个资源池的基本边栏选项卡分别提供一个图表，其中包含该资源池的度量值。就像 ASE 边栏选项卡中的图表，你可以进入图表并设置所需的警报。从 ASE 边栏选项卡为特定的资源池设置警报，效果等同于从资源池进行设置。在辅助角色池的“设置”边栏选项卡中，你有权列出在此辅助角色池中运行的所有应用或 App Service 计划。

![][5]

### 门户缩放功能  

共有三种缩放操作：

- 更改 ASE 中可供 IP SSL 使用的 IP 地址数
- 更改用于资源池中的计算资源大小
- 以手动方式或通过自动缩放更改用于资源池中的计算资源数

门户中提供三种方式用于控制资源池中的服务器数

- 顶部主要 ASE 边栏选项卡中的缩放操作。你可以对前端和辅助角色池进行多项缩放配置更改，它们全部将以单个操作的方式应用。
- 单个资源池的“缩放”边栏选项卡中的手动缩放操作（位于“设置”下）
- 可从单个资源池的“缩放”边栏选项卡设置的自动缩放

若要使用 ASE 边栏选项卡上的缩放操作，请将滑块拖到所需的数量并保存。此 UI 还支持更改大小。

![][6]

若要使用特定资源池中的手动或自动缩放功能，请根据需要转到“设置”>“前端池”/“辅助角色池”，然后打开想要更改的池。转到“设置”>“扩大或设置”>“增加”。“扩大”边栏选项卡可让你控制实例数量。“增加”可让你控制资源大小。

![][7]

## 容错注意事项

可对 Azure 环境进行配置以使用最多 55 个总计算资源。在这 55 个计算资源中，只有 50 个可用于承载工作负荷。原因在于两个方面。至少有 2 个前端计算资源。还剩最多 53 个来支持辅助池分配。为了提供容错功能，还需根据以下规则分配额外计算资源：

- 每个辅助角色池至少需要一个额外计算资源，该资源不能是已分配的工作负荷
- 当辅助角色池中计算资源的数量超出特定值时，则需要另一个计算资源以实现容错。在前端池中不需要这样做。

在任何单个辅助池中，对于分配到辅助池的给定 X 个资源，容错要求如下：

- 如果 X 介于 2 到 20，则可用于工作负荷的可用计算资源量为 X-1
- 如果 X 介于 21 到 40，则可用于工作负荷的可用计算资源量为 X-2
- 如果 X 介于 41 到 53，则可用于工作负荷的可用计算资源量为 X-3

最少的使用量具有 2 个前端服务器和 2 个辅助角色。上述说明可通过几个示例来澄清。

- 如果在单个池中有 30 个辅助角色，则其中有 28 个可用于托管工作负荷。 
- 如果在单个池中有 2 个辅助角色，则有 1 个可用于托管工作负荷。
- 如果在单个池中有 20 个辅助角色，则有 19 个可用于托管工作负荷。  
- 如果在单个池中有 21 个辅助角色，则同样只有 19 个可用于托管工作负荷。  

容错是很重要的环节，但在缩放超过特定阈值时必须留意。如果想要从 20 个实例添加更多容量，请缩放到 22 或更高，因为 21 并不额外添加任何容量。缩放为 40 以上时也是一样，下一个添加容量的数为 42。

## 删除 Azure 环境 ##

如果想要删除 Azure 环境，使用“Azure 环境”边栏选项卡顶部的“删除”操作即可。当你执行此操作时，系统将提示你输入 Azure 环境的名称，以确认你确实想要执行此操作。注意：删除 Azure 环境时，将同时删除其中包含的所有内容。

![][9]

## 入门
[应用程序服务环境自述文件](/documentation/articles/app-service-app-service-environments-readme)中提供了有关 Azure 环境的所有文章和操作说明。

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

<!---HONumber=Mooncake_0627_2016-->