---
title: 创建可商用的 Azure 远程渲染应用程序
description: 有关创建使用 Azure 远程渲染的可商用应用程序的策略和注意事项
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0dad78ad76a870ea9f1db28a3cb5ccace5cd804f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510923"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>教程：创建可商用的 Azure 远程渲染应用程序

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 商业应用程序的会话管理
> * 跟踪计费会话
> * 优化用户在会话加载时间方面的体验
> * 有关网络延迟的注意事项

## <a name="prerequisites"></a>先决条件

* 本教程基于[教程：保护 Azure 远程渲染和模型存储](../security/security.md)编写而成。

## <a name="introduction-to-commercial-readiness"></a>商业就绪简介

Azure 远程渲染拓宽了混合现实的用途。 将基本要素集成到解决方案后，你还需要额外考虑一些注意事项，才能确保解决方案安全无忧、可缩放且立即可提供价值。

在本模块中，将额外介绍一些你可能需要为商业应用程序考虑的功能。

如需全系统体系结构最佳做法的全面概述，请访问：

* [Azure 体系结构中心](https://docs.microsoft.com/azure/architecture/)
* [Azure 开发人员入门指南](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)

## <a name="analytics"></a>分析

集成分析工具有助于管理、跟踪和改进解决方案。

有关可用分析资源的完整列表，请访问：

* [Azure 分析服务](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>跟踪计费使用情况

跟踪多个内部团队或外部客户端的 Azure 远程渲染使用情况成为了一项重要的考虑因素，在多租户情况下尤其如此。

为此，Azure 提供了一项名为“资源标记”的服务，它将 Azure 远程渲染服务的使用情况与每个客户端关联起来。

要详细了解资源命名和标记，最好先查看：

* [资源命名和标记决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

### <a name="diagnostics"></a>诊断

借助 Windows 事件跟踪 (ETW) 和事件跟踪日志记录 (ETL) 等功能强大的工具，可在应用程序中轻松生成跟踪事件，还可帮助诊断网络、内容引入、会话、应用程序及商业解决方案部署过程中可能出现的其他问题。

有关详细信息，请访问：

* [创建客户端性能跟踪](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)
* [如何收集 Windows 事件跟踪 (ETW) 数据](https://docs.microsoft.com/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [使用 Windows 设备门户：日志记录](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>使用情况分析

Azure Application Insights 有助于了解用户如何使用你的 Azure 远程渲染应用程序。 每次更新应用时，你都可评估应用在用户那里的运行状况并相应地改进解决方案。 了解这些信息后，你便可针对下一开发周期做出数据驱动的决策。

有关详细信息，请访问：

* [Application Insights 使用分析](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

## <a name="fast-startup-time-strategies"></a>快速启动策略

你的用例可能需要你快速启动应用程序并查看 3D 模型。 例如，在重要的会议期间，必须提前启动和运行所有内容。 另外一个例子是在 CAD 3D 模型评审期间，能否在 CAD 应用程序与混合现实之间实现设计的快速迭代对效率来说至关重要。

Azure 远程渲染需要预处理的 3D 模型，而 Azure 当前需要几分钟的时间来创建会话并加载用于渲染的模型。 要尽可能快速、顺畅地完成此过程，你需要提前准备 3D 模型数据和 ARR 会话。

这里分享的建议当前不是标准 Azure 远程渲染的一部分，但你可自行实现来缩短启动时间。

### <a name="initiate-early"></a>尽早启动

要缩短启动时间，最简单的解决方案是尽早在用户工作流中创建和初始化会话。 一种策略是在知道将需要 ARR 会话后立即初始化会话。 通常是在用户开始将 3D 模型上传到 Azure Blob 存储来与 Azure 远程渲染一起使用时进行此操作。 这样的话，可在上传 3D 模型的同时开始创建并初始化会话，让这两个工作流同时运行。

你还可通过确保所选的 Azure Blob 存储输入和输出容器与 Azure 远程渲染会话位于同一区域级数据中心，来进一步简化此过程。

### <a name="scheduling"></a>计划

如果你知道将来需要 Azure 远程渲染，你可计划一个具体的日期和时间来启动 Azure 远程渲染会话。

此选项可通过 Web 门户提供，用户可在这里上传 3D 模型并计划未来的查看时间。 也很适合在这里要求其他首选项，例如[标准](../../../reference/vm-sizes.md)或[高级](../../../reference/vm-sizes.md)渲染 。 如果需要显示资产组合，其中更难自动确定理想大小或需要确保 Azure 区域在指定时间有可用的 VM，则可能适合使用高级渲染。

### <a name="session-pooling"></a>会话池

在要求最严苛的情况下，还有一个选项是会话池，你可随时在这里创建和初始化一个或多个对话。 这将创建一个会话池，供发出请求的用户立即使用。 该方法的缺点是，VM 一旦初始化，服务就开始产生费用。 让会话池始终保持运行状态可能并不划算，但它可根据分析数据预测峰值负载，也可与上述计划策略相结合，预测何时需要会话并相应地增大和缩小会话池。

此策略还有助于更灵活地优化标准会话与高级会话之间的选择，因为在一个用户会话内切换这两种类型的速度要快得多，例如先查看高级复杂性模型，然后再查看可在标准会话中使用的模型   。 如果这些用户会话很长，则可大幅节省成本。

要详细了解 Azure 远程渲染会话，请参阅：

* [远程渲染会话](https://docs.microsoft.com/azure/remote-rendering/concepts/sessions)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>标准与高级服务器大小路由策略

在设计用户体验和端到端系统时，需要选择是创建标准服务器大小还是高级服务器大小是一道难题 。 尽管可选择仅使用高级会话，但标准会话使用的 Azure 计算资源少得多，且价格比高级会话的低  。 因此，应尽可能使用标准会话，仅在需要时使用高级会话 。

我们在这里分享了几个选项（范围由窄到宽），用于解决管理会话选择的需求。

### <a name="use-only-standard-or-premium"></a>仅使用标准会话或高级会话

如果你确信需求始终低于标准会话与高级会话之间的阈值，则这会大大简化决策过程  。 仅使用标准会话。 但请记住，如果已加载资产的整体复杂性对于标准会话来说太过复杂而被拒绝，则会对用户体验造成很大影响。

同样，如果你预计需求很大，超过了超过标准会话与高级会话之间的阈值，或者在你的使用情况中成本不是关键因素，那么可始终选择高级会话来简化决策  。

### <a name="ask-the-user"></a>询问用户

如果希望同时支持标准会话和高级会话，那么确定要实例化的会话类型的最简单的方法是，在用户选择 3D 资产进行查看时询问用户 。 这种方法的问题是，它要求用户了解要查看的 3D 资产（甚至是多个资产）的复杂性。 因此，通常不建议这样做。 如果用户选择错误，选择了标准会话，则由此获得的用户体验可能会在不当的时候受到影响。

### <a name="analyze-the-3d-model"></a>分析 3D 模型

另一种相对简单的方法是分析所选 3D 资产的复杂性。 如果模型复杂性低于标准会话阈值，则启动标准会话；否则启动高级会话  。 这里的问题是，一个会话最终可能用于查看多个模型，其中某些模型可能超过标准会话的复杂性阈值，从而导致无法为一系列不同的 3D 资产无缝使用同一会话。

### <a name="automatic-switching"></a>自动切换

在还包含会话池的系统设计中，合理的做法是在标准会话与高级会话之间自动切换 。 此策略可进一步优化资源利用率。 当用户加载模型进行查看时，复杂性已经确定，而且系统会从会话池服务中请求正确的会话大小。

## <a name="working-with-networks"></a>使用网络

### <a name="diagnostics"></a>诊断

Azure 远程渲染需要快速、低延迟的 Internet 连接。 用户的网络质量会对体验质量产生重大影响。 假设你的客户端可能有不同的网络配置，偶尔才会出现网络延迟较高的情况，那么诊断工具很关键。  

为了确保你可提供一致的优质体验，建议你将服务器端和客户端分析工具集成到 Azure 远程渲染应用程序中。 这样一来，你就可获得诊断和缓解客户端可能遇到的任何网络问题所需的信息。

### <a name="client-network-configurations"></a>客户端网络配置

在开发部署到各种企业环境的强大协作解决方案时，最大的挑战之一是为客户端可能使用的不同网络拓扑和企业防火墙配置做好准备。

许多企业会阻止 LAN 内的所有对等流量。 在所有已发现的混合现实应用程序实例之间建立本地共享会话时，这样很难充分利用自动 LAN 发现的简捷性和简化的 UX。

其他潜在的故障点是配置为故意限制带宽的路由器和阻止大多数 TCP/IP 端口的防火墙。

每次你计划在不熟悉的网络上使用 Azure 远程渲染时，都建议你执行以下操作：

* 提供会前清单来评估网络就绪情况。
* 确保相应的区域级数据中心可处理请求。
* 留出足够多的时间来诊断任何问题。
* 将采用高带宽数据计划的移动热点作为备份。

### <a name="end-to-end-bandwidth"></a>端对端带宽

请务必评估 Azure 远程渲染 VM 和最终客户端之间可能存在的每个网络分段的带宽能力。 请记住，从 ISP 到客户端的网络分段相比，从 Azure 数据中心到客户端 ISP 的网络分段的限制性可能更强。 Blob 下载速度测试可用于帮助诊断此类问题。

### <a name="bandwidth-competition"></a>带宽争用

在设计混合现实应用程序时，请记住，应用的不同功能可能会与 Azure 远程渲染争用带宽。 最可能的意外情况是，一间会议室中的多名参与者都期望同时使用 ARR 来查看 3D 资产。 网络数据流的每个分段都需要能够传输所有 ARR 数据合并后的总量。

其他示例包括流式视频、语音聊天以及后台同时上传其他相关内容，在参与者很多且系统使用的是分布式对等方法（而不是在中间方法中采用混音服务器）的情况下尤为如此。

要详细了解网络分析，请查看：

* [Azure 存储 Blob 下载速度测试](https://www.azurespeed.com/Azure/Download)
* [Azure 网络往返行程延迟统计信息](https://docs.microsoft.com/azure/networking/azure-network-latency)
* [服务器端性能跟踪](https://docs.microsoft.com/azure/remote-rendering/overview/features/performance-queries)
* [客户端性能跟踪](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)

## <a name="collaboration-considerations"></a>协作注意事项

Azure 远程渲染的一些最有价值的用途涉及到多名参与者在同一时间查看相同的 3D 体验时进行协作。 在这些共享会话中，必须意识到，不管每位参与者是否位于同一网络的同一位置，他们都需要唯一的 Azure 远程渲染会话。

这是因为每位参与者实际上从不同的优势位置看到相同的体验，这就需要从其中每个角度同时渲染相同的 3D 资产。

### <a name="multiple-azure-remote-rendering-sessions"></a>多个 Azure 远程渲染会话

如果打算支持 Azure 远程渲染共享体验，则需要准备好用于创建和管理 ARR 会话的系统，以启动多个会话。 如果参与者分散在不同的地理位置，则这些会话可能需要在不同的 Azure 数据中心中进行初始化。

系统还必须管理这样一种可能的情况，即一名或多名参与者所在的地理区域可能当前并未受到 Azure 远程渲染的支持，或者当前没有可用的 Azure 远程渲染 VM 实例。

你可结合会话池和本文讨论的其他策略，进一步简化对多个并发会话的管理。

### <a name="azure-blob-storage-considerations"></a>Azure Blob 存储注意事项

所有同时发生的 ARR 会话都可引用相同的 SAS URI，以便你查看转换后的模型。 这样就可将所需的 3D 资产上传和转换一次，然后在所有会话中进行共享。 当多名参与者同地协作并使用同一数据中心，而没有与 Azure Blob 存储（位于与 Azure远程渲染服务器和用户不同的数据中心）相关的性能问题时，这样尤其有用。

如果 3D 资产通常是上传用于一个查看会话且随后被丢弃（例如在设计评审会话中），那么 Azure Blob 存储相对于 Azure 远程渲染服务器的地理区域也不太重要。

但对于将重复使用的 3D 资产（例如在训练用例中），建议在计划使用 Azure 远程渲染的每个区域级数据中心的 Blob 存储中保留现成的 3D 资产。 此操作可通过 Azure 存储冗余自动完成。 CDN 通常也用于此目的，但它尚无法用于 Azure 远程渲染。

有关详细信息，请参阅：

* [混合现实中的共享体验](https://docs.microsoft.com/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

## <a name="managing-model-access"></a>管理模型访问权限

要充分利用 Azure 远程渲染，你需要仔细考虑用于管理 3D 模型的端到端基础结构。

使用 Azure 远程渲染的优点是，查看之前永远不需要将大型 3D 资产直接传输到混合现实设备。  此外，一旦上传和转换 3D 资产用于 Azure 远程渲染，任何数量的用户都可共用该 3D 模型的一个实例。

### <a name="considerations-for-3d-model-access"></a>有关 3D 模型访问的注意事项

下面是决定模型访问策略时的几个关键注意事项。

根据预期用例，确定最佳位置或位置组合，以便用户能够选择要查看的 3D 资产。 一些常见选项包括：

* 直接在混合现实体验中
* 通过配套的 Web 门户
* 在配套的桌面或移动应用程序中

如果你的用例的使用模式是可能多次上传同一 3D 资产，那么后端将跟踪已转换哪些模型来用于 ARR，使模型在将来的多次选择中仅被预处理一次。 设计评审的一个例子是：团队可访问常见的原始 3D 资产。 每位团队成员都应使用 ARR 在其工作流的某个环节来查看模型。 只有第一个视图才会触发预处理步骤。 后续视图将在 SAS 输出容器中查找处理后的关联文件。

根据具体用例，你可能需要为每个要在同一会话中同时查看的每项 3D 资产或一组资产确定并保留正确的 Azure 远程渲染服务器大小（标准或高级） 。  

### <a name="on-device-model-selection-list"></a>设备端模型选择列表

在许多用例中（例如培训、任务指导或营销应用），通常要在 Azure 远程渲染中查看的一组 3D 资产可能十分静态。 在这些情况下，可预先转换一组精选的 3D 资产，通过包含必要信息（用于填充精选的资产选择列表）的数据库提供它们。 然后，通过混合现实应用程序检索这些数据，以填充选择菜单。

还可更进一步：另外提供一种方法来上传专用 3D 资产（这些资源对每个人或每个组而言都是唯一的）。 然后，在选择要查看的 3D 资产的用户体验中，该专用资产列表可与常见的精选资产列表合并在一起。

### <a name="on-device-onedrive-access"></a>设备端 OneDrive 访问

考虑到 OneDrive 文件选取器是本机内置在 Microsoft 的混合现实设备中的，因此从 OneDrive 选择设备端 3D 资产非常有吸引力，尤其适合常常需要加载不同或经过修改的 3D 模型的用例。 在此方案中，用户将通过混合现实应用程序内的 OneDrive 文件选取器选择一项或多项 3D 资产。 然后，3D 资产将迁移到 SAS 输入容器，转换为 SAS 输出容器，并附加到 ARR 会话。 理想情况下，混合现实应用程序会调用基于云的进程来执行这些步骤，而不是将所有资产从 OneDrive 移动到设备，然后再转到 Azure Blob 存储。

通过使之前查看过的 3D 资产持续保持关联，可将此方法再推进一步，这样在从 OneDrive 再次选择相同的模型时，应用程序就可绕过转换过程，通过其 SAS URI 直接加载已转换的相关的 3D 资产。

有关详细信息，请参阅：

* [用于从 OneDrive 复制到 Azure 存储的 Microsoft Power Automate 模板](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [OneDrive 文件存储 API 概述](https://docs.microsoft.com/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>直接 CAD 访问

在混合现实中，一个极具吸引力的用例是对正在进行的 CAD 进行设计评审。 在该场景中，关键是从桌面到混合现实的加载时间要最快。 理想的解决方案可能涉及到开发特定 CAD 应用程序的插件。 这些插件将直接管理负载、转换和查看过程的各个方面：

* 提供 UX 用于以下目的：
  * 将 CAD 应用程序与特定的混合现实设备配对（一次）。
  * 请求在该混合现实设备上查看所选几何图形。
* 如果尚未运行，则启动 Azure 远程渲染会话，以便它可在上传和转换 CAD 文件时并行处理
* 将 CAD 几何数据标准化为 Azure 远程渲染支持的一种格式
* 将经过标准化处理的数据直接传输到 Azure Blob 存储输入容器
* 启动模型转换过程
* 将模型的输出容器 SAS URI 关联到 Azure 远程渲染会话
* 通知配对的混合现实应用程序该模型可用且可供查看，并提供输出容器 SAS URI，使应用程序可将其附加到会话中。

有一个更简单但不那么精简的方法可自动执行以下操作：将 3D 模型保存到本地硬盘，然后启动一个进程，将保存的文件传输到 SAS 输入容器。

### <a name="azure-marketplace"></a>Azure 市场

许多企业客户端出于安全原因，要求在他们自己的 Azure 帐户和凭据下部署 Azure Stack。 为此，建议你打包 Azure 托管应用程序，以便能将其作为 Azure 应用程序产品/服务发布到 Azure 市场。

有关详细信息，请参阅：

* [Azure 市场](https://azure.microsoft.com/marketplace/)
* [教程：在市场中发布 Azure 托管应用程序](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-marketplace-app)

### <a name="security"></a>安全性

从头开始构建端到端 Azure 远程渲染解决方案以实现安全性非常重要。 在设计端到端解决方案时，需要考虑许多安全问题，其中包括：

* 身份验证策略
* 访问管理 - 组、策略和权限
* 多租户
* 数据存储和传输加密
* 临时使用令牌
* 分布式拒绝服务 (DDoS) 攻击
* 威胁检测
* VPN 和安全网络
* 防火墙
* 证书和密钥管理
* 应用程序漏洞和攻击

对于身份验证，最好尽量将 ARR 身份验证和会话管理迁移到 Azure Web 服务。 这会使解决方案更安全且更易于管理。

有关详细信息，请参阅：

* [Azure AD 服务身份验证](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-service-authentication)
* [使用 Azure 加强安全态势](https://azure.microsoft.com/overview/security/)
* [云安全](https://azure.microsoft.com/product-categories/security/)
