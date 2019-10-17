---
title: Microsoft Azure FXT Edge Filer 群集创建
description: 如何使用 Azure FXT Edge Filer 创建混合存储缓存群集
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 54d70f60d4b7290b60c864817c756648fef1f481
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256084"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>教程：创建 Azure FXT Edge Filer 群集

为缓存安装并初始化 Azure FXT Edge Filer 硬件节点后，使用 FXT 群集软件创建缓存群集。 

本教程将指导你完成将硬件节点配置为群集的步骤。 

本教程介绍： 

> [!div class="checklist"]
> * 开始创建群集之前所需的信息
> * 群集的管理网络、群集网络和面向客户端的网络之间的区别
> * 如何连接到群集节点 
> * 如何使用一个 Azure FXT Edge Filer 节点创建初始群集
> * 如何登录群集控制面板以配置群集设置

此过程需要 15 到 45 分钟，具体取决于识别 IP 地址和网络资源所需要的工作量。

## <a name="prerequisites"></a>先决条件

在开始本教程前，请完成以下先决条件：

* 在数据中心内安装 Azure FXT Edge Filer 硬件系统 

  只需一个节点即可创建群集，但需要[添加至少两个节点](fxt-add-nodes.md)才能配置群集并使其可供使用。 

* 将相应的电源和网络电缆连接到系统  
* 启动至少一个 Azure FXT Edge Filer 节点并[设置其根密码](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>为群集收集信息 

需要以下信息才能创建 Azure FXT Edge Filer 群集：

* 群集名称

* 要为群集设置的管理密码

* IP 地址：

  * 用于群集管理的单个 IP 地址，以及用于管理网络的网络掩码和路由器
  * 用于群集（节点到节点）通信的连续 IP 地址范围中的第一个和最后一个 IP 地址。 有关详细信息，请参阅下面的 [IP 地址分发](#ip-address-distribution)。 
  * （面向客户端的 IP 地址在创建群集后设置。）

* 网络基础结构信息：

  * 群集的 DNS 服务器的 IP 地址
  * 群集的 DNS 域的名称
  * 群集 NTP 服务器的名称或 IP 地址（一台服务器，或三台或更多服务器） 
  * 是否想要在群集接口上启用 IEEE 802.1AX-2008 链接聚合
  * 如果启用链接聚合，那么是否使用 IEEE 802.3ad (LACP) 动态聚合

可以在创建群集后配置这些网络基础结构项目，但最好在创建时执行此操作。 

### <a name="ip-address-distribution"></a>IP 地址分发

Azure FXT Edge Filer 混合存储缓存群集使用三种 IP 地址：

* 管理 IP：用于群集管理的单个 IP 地址

  此地址充当访问群集配置实用程序（基于 Web 的控制面板或 XML-RPC API）的入口点。 此地址自动分配给群集中的主节点，并在主节点更改时自动移动。

  其他 IP 地址可用于访问控制面板，但管理 IP 地址在即使单个节点故障转移的情况下也可提供访问。

* 群集网络：用于群集通信的 IP 地址范围

  群集网络用于群集节点之间的通信，以及从后端存储（核心文件管理器）检索文件。

  **最佳做法：** 在每个 Azure FXT Edge Filer 节点上为每个用于群集通信的物理端口分配一个 IP 地址。 群集会自动将指定范围内的地址分配给各个节点。

* 面向客户的网络：客户端用于请求和写入文件的 IP 地址范围

  客户端使用客户端网络地址来通过群集访问核心文件管理器数据。 例如，NFS 客户端可能会装载其中一个地址。

  **最佳做法：** 在每个 FXT 系列节点上为每个用于客户端通信的物理端口分配一个 IP 地址。

  群集尽可能均匀地跨其组成节点分发面向客户端的 IP 地址。

  为简单起见，许多管理员使用轮询机制 DNS (RRDNS) 配置来配置单个 DNS 名称，以便更轻松地在地址范围内分发客户端请求。 此设置还允许所有客户端使用同一装载命令来访问群集。 有关详细信息，请阅读[配置 DNS](fxt-configure-network.md#configure-dns-for-load-balancing)。

必须指定管理 IP 地址和群集网络地址范围才能创建新的群集。 面向客户端的地址在创建群集后指定。

## <a name="connect-to-the-first-node"></a>连接到第一个节点

可以连接到任何已安装的 FXT 节点，并使用其操作系统软件来设置群集。

如果尚未执行此操作，请为群集启动至少一个 FXT 节点，并确保其具有网络连接和 IP 地址。 必须设置新的根密码才能激活节点，因此，如果尚未执行此操作，请按照[设置硬件密码](fxt-node-password.md)中的步骤执行操作。

若要检查网络连接，请确保节点的网络链接 LED 指示灯亮起（如有必要，其连接到的网络交换机上的指示灯也应亮起）。 LED 指示灯在[监视 Azure FXT Edge Filer 硬件状态](fxt-monitor.md)中进行介绍。

节点启动时，它将请求 IP 地址。 如果它连接到 DHCP 服务器，则会接受 DHCP 提供的 IP 地址。 （此 IP 地址为临时地址， 将在创建群集时更改。）

如果它未连接到 DHCP 服务器或未收到响应，则该节点将使用 Bonjour 软件以 169.254.\*.\* 的格式设置自分配 IP 地址。 但是，应先在其中一个节点的网卡上设置临时静态 IP 地址，然后再使用它来创建群集。 此旧文档包含相关说明；请与 Microsoft 客户服务和支持部门联系，以获取更新的信息：[附录 A：在 FXT 节点上设置静态 IP 地址](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)。

### <a name="find-the-ip-address"></a>查找 IP 地址

连接到 Azure FXT Edge Filer 节点以查找其 IP 地址。 可以使用串行电缆、直接连接到 USB 和 VGA 端口，或通过 KVM 交换机连接。 （有关端口连接的详细信息，请参阅[设置初始密码](fxt-node-password.md)。）

连接后，使用用户名 `root` 和首次启动节点时设置的密码登录。  

登录后，需要确定节点的 IP 地址。

使用 `ifconfig` 命令查看分配给此系统的地址。

例如，`ifconfig | grep -B5 inet` 命令会搜索具有 Internet 地址的端口，并提供五行上下文以显示端口标识符。

记下 ifconfig 报告中显示的任何 IP 地址。 使用端口名称（如 e0a 或 e0b）列出的地址是不错的选择。 请勿使用 e7* 名称列出的任何 IP 地址，因为这些名称仅用于 iDRAC/IPMI 服务端口。  

## <a name="load-the-cluster-configuration-wizard"></a>加载群集配置向导

使用基于浏览器的群集配置工具来创建群集。 

在 Web 浏览器中输入节点的 IP 地址。 如果浏览器提供有关该站点不受信任的消息，请仍然继续访问该站点。 （单个 Azure FXT Edge Filer 节点没有 CA 提供的安全证书。）

![浏览器窗口中的控制面板登录页](media/fxt-cluster-create/unconfigured-node-gui.png)

将“用户名”和“密码”字段留空   。 单击“登录”以加载群集创建页  。

![基于浏览器的 GUI 控制面板中的未配置节点的初始设置屏幕。 它显示用于更新软件、手动配置群集或通过安装程序文件配置群集的选项。](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>创建群集

群集配置工具将引导完成一组用于创建 Azure FXT Edge Filer 群集的屏幕。 确保在开始之前准备好[所需信息](#gather-information-for-the-cluster)。 

### <a name="creation-options"></a>创建选项

第一个屏幕提供三个选项。 除非支持人员有特别说明，否则请使用手动配置选项。

单击“我将手动配置群集”以加载新的群集配置选项屏幕  。 

其他选项很少用到：

* “更新系统映像”会提示你在创建群集之前安装新的操作系统软件。 （当前安装的软件版本在屏幕顶部列出。）必须提供软件包文件 - URL 和用户名/密码，或通过从计算机上传文件。 

* Microsoft 客户服务和支持部门有时使用群集安装程序文件选项。 

## <a name="cluster-options"></a>群集选项

下一个屏幕将提示你配置用于新群集的选项。

该页分为两个主要部分：“基本配置”和“网络配置”   。 “网络配置”部分还包含子部分：一个用于**管理**网络，另一个用于**群集**网络。

### <a name="basic-configuration"></a>基本配置

在顶部，填写新群集的基本信息。

![浏览器 GUI 页中“基本配置”部分的详细信息。 它显示三个字段（群集名称、管理员密码、确认密码）](media/fxt-cluster-create/basic-configuration.png) 

* “群集名称”- 输入群集的唯一名称  。

  群集名称必须符合以下条件：
  
  * 长度为 1 到 16 个字符
  * 可以包含字母、数字、短划线 (-) 和下划线 (_) 字符 
  * 不得包含其他标点符号或特殊字符
  
  可以稍后在“群集” > “常规设置”配置页上更改此名称   。 （有关群集设置的详细信息，请阅读[群集配置指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)，该指南不属于本文档集。）

  > [!NOTE] 
  > 群集名称用于标识为支持监视或故障排除而上传的系统信息，因此包含公司名称会很有帮助。

* “管理员密码”- 为默认管理用户 `admin` 设置密码  。
  
  应该为管理群集的每个人设置单独的用户帐户，但不能删除用户 `admin`。 如果需要创建其他用户，请以 `admin` 身份登录。
 
  可以在群集控制面板的“管理” > “用户”设置页中更改 `admin` 的密码   。 有关详细信息，请阅读[群集配置指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html)中的**用户**文档。

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>网络配置

**网络**部分会提示你指定群集将使用的网络基础结构。 

有两个单独的网络可供配置：

* *管理网络*提供对群集的管理员访问权限，以进行配置和监视。 连接到控制面板或进行 SSH 访问时使用此处指定的 IP 地址。 

  大多数群集仅使用单个管理 IP 地址，但如果想要添加接口，则可以在创建群集后执行此操作。

* *群集网络*用于群集节点之间以及群集节点和后端存储（核心文件管理器）之间的通信。

面向客户端的网络在稍后进行配置，即创建群集后。

本部分还包括两个网络使用的 DNS 和 NTP 服务器的配置。

### <a name="configure-the-management-network"></a>配置管理网络

“管理”部分中的设置适用于提供对群集的管理员访问权限的网络  。

![“管理”部分的详细信息，包含 5 个选项的相关字段和 1Gb 管理网络的复选框](media/fxt-cluster-create/management-network-filled.png)

* “管理 IP”- 指定用于访问群集控制面板的 IP 地址  。 此地址将由群集的主节点声明，但如果原始主节点变得不可用，它将自动移至正常节点。

  大多数群集仅使用一个管理 IP 地址。 如果需要多个管理 IP 地址，可以在使用“群集” > “管理网络”设置页创建群集后添加它们   。 阅读[群集配置指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html)，了解详细信息。

* “网络掩码”- 指定管理网络的网络掩码  。

* “路由器”- 输入管理网络使用的默认网关地址  。

* “VLAN 标记(可选)”- 如果群集使用 VLAN 标记，请指定管理网络的标记  。

  在“群集” > “VLAN”设置页中配置其他 VLAN 设置   。 有关详细信息，请阅读“群集配置指南”中的[使用 VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) 和[群集 > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html)。

* “MTU”- 如有必要，调整群集管理网络的最大传输单元 (MTU)  。

* “使用 1Gb 管理网络”- 如果想要将 FXT 节点上的两个 1GbE 网络端口仅分配给管理网络，请选中此框  。 （必须为所有其他流量提供 25GbE/10GbE 端口。）如果不选中此框，管理网络将使用可用的最高速度端口。 

### <a name="configure-the-cluster-network"></a>配置群集网络 

群集网络设置适用于群集节点之间以及群集节点和核心文件管理器之间的流量。

![“群集”部分的详细信息，包含用于输入六个值的字段](media/fxt-cluster-create/cluster-network-filled.png)

* “第一个 IP”和“最后一个 IP”- 输入定义用于内部群集通信的范围的 IP 地址   。 此处使用的 IP 地址必须是连续的，不能由 DHCP 分配。

  可以在创建群集后添加更多 IP 地址。 使用“群集” > “群集网络”设置页（[群集配置指南文档](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)）   。

  “范围内 IP 地址数”中的值将自动计算并显示  。

* “非管理网络掩码(可选)”- 指定群集网络的网络掩码  。 

  系统会自动建议为管理网络输入的网络掩码值；可根据需要更改。

* “群集路由器(可选)”- 指定群集网络使用的默认网关地址  。 

  系统会自动建议为管理网络提供的同一网关地址。

* “群集 VLAN 标记(可选)”- 如果群集使用 VLAN 标记，请指定群集网络的标记  。

* “非管理 MTU (可选)”- 如有必要，调整群集网络的最大传输单元 (MTU)  。

### <a name="configure-cluster-dns-and-ntp"></a>配置群集 DNS 和 NTP 

“群集”部分提供用于指定 DNS 和 NTP 服务器以及用于启用链接聚合的字段  。 这些设置适用于群集使用的所有网络。

![DNS/NTP 配置部分的详细信息，包括用于 DNS 服务器的三个字段、用于 DNS 域和 DNS 搜索的字段、用于 NTP 服务器的三个字段以及链接聚合选项的下拉菜单](media/fxt-cluster-create/dns-ntp-filled.png)

* “DNS 服务器”- 输入一个或多个域名系统 (DNS) 服务器的 IP 地址  。

  建议为所有群集使用 DNS，且如果想要使用 SMB、AD 或 Kerberos，则需要提供 DNS。 
  
  若要获得最佳性能，请按照[为 Azure FXT Edge Filer 群集配置 DNS](fxt-configure-network.md#configure-dns-for-load-balancing) 中所述配置群集的 DNS 服务器，以进行轮询机制负载均衡。

* “DNS 域”- 输入群集将使用的网络域名  。

* “DNS 搜索”-（可选）输入系统应搜索用于解析 DNS 查询的其他域名  。 最多可以添加六个域名（以空格分隔）。

* “NTP 服务器”- 在提供的字段中指定一个或三个网络时间协议 (NTP) 服务器  。 可以使用主机名或 IP 地址。

* “链接聚合”- 链接聚合允许自定义群集 FXT 节点上的以太网端口处理各类流量的方式  。 若要了解详细信息，请阅读“群集配置指南”中的[链接聚合](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation)。

### <a name="click-the-create-button"></a>单击“创建”按钮

在表单中提供所有必需的设置后，单击“创建群集”按钮  。

![填写完毕的表单的底部，光标位于右下方的“创建”按钮上](media/fxt-cluster-create/create-cluster.png)

系统在创建群集时会显示一条消息。

![浏览器中的群集配置状态消息：“FXT 节点现在正在创建群集。 这需要几分钟。 创建群集后，请访问此链接以完成配置。” “访问此链接”显示超链接](media/fxt-cluster-create/creating-message.png)

片刻后，可以单击消息中的链接转到群集控制面板。 （此链接会转到在“管理 IP”中指定的 IP 地址  。）单击“创建”按钮后，链接需要 15 秒到 1 分钟才能变为活动状态。 如果 Web 界面未加载，请再等待几秒钟，然后再次单击该链接。 

创建群集需要一分钟或更长时间，但可以在流程进行的过程中登录控制面板。 在群集创建过程完成之前，控制面板的仪表板页显示警告为正常现象。 

## <a name="open-the-settings-pages"></a>打开“设置”页 

创建群集后，需要自定义其网络和工作流的配置。 

使用控制面板 Web 界面设置新的群集。 单击群集创建状态屏幕中的链接，或浏览到在群集上设置的管理 IP 地址。

使用用户名 `admin` 和创建群集时设置的密码登录 Web 界面。

![显示控制面板登录字段的 Web 浏览器](media/fxt-cluster-create/admin-login.png)

控制面板将打开并显示“仪表板”页  。 群集创建完成后，所有警告消息应该会从显示屏中清除。

单击“设置”选项卡以配置群集  。

在“设置”选项卡上，左侧边栏显示配置页面菜单  。 页面按类别进行组织。 单击类别名称顶部的 + 或 - 控件可展开或隐藏各个页面。

![控制面板的“设置”选项卡（在浏览器中），且已加载“群集”>“常规设置”页](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>群集设置步骤

在这一流程环节，群集已存在，但它只有一个节点，且没有面向客户端的 IP 地址，也没有后端存储。 从新创建的群集变为准备好处理工作流的缓存系统，需要执行其他设置步骤。

### <a name="required-configuration"></a>所需配置

大多数或所有群集都需要执行这些步骤。 

* 向群集添加节点 

  三个节点为标准配置，但许多生产群集具有更多节点 - 最多 24 个节点。

  阅读[添加群集节点](fxt-add-nodes.md)，了解如何将其他 Azure FXT Edge Filer 单元添加到群集，以及如何启用高可用性。

* 指定后端存储

  为群集将使用的每个后端存储系统添加*核心文件管理器*定义。 有关详细信息，请阅读[添加后端存储并配置虚拟命名空间](fxt-add-storage.md#about-back-end-storage)。

* 设置客户端访问和虚拟命名空间 

  创建至少一个虚拟服务器 (vserver) 并为其分配 IP 地址范围以供客户端计算机使用。 还必须配置群集命名空间（有时称为全局命名空间或 GNS），这是一种虚拟文件系统功能，可用于将后端存储导出到虚拟路径。 即使切换后端存储介质，群集命名空间也可为客户端提供一致且可访问的文件系统结构。 命名空间还可以为 Azure Blob 容器或其他受支持的云对象存储提供用户友好的虚拟存储层次结构。

  有关详细信息，请阅读[配置命名空间](fxt-add-storage.md#configure-the-namespace)。 此步骤包括：
  * 创建 vserver
  * 设置客户端网络视图和后端存储之间的联接 
  * 定义每个 vserver 服务的客户端 IP 地址

  > [!Note] 
  > 建议在开始设置群集的 GNS 之前进行充分规划。 阅读“群集配置指南”中的[使用全局命名空间](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)和[创建和使用 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) 部分以获取帮助。

* [调整网络设置](fxt-configure-network.md)

  有多个与网络相关的设置应针对新群集进行验证或自定义。 有关这些项目的详细信息，请阅读[调整网络设置](fxt-configure-network.md)：

  * 验证 DNS 和 NTP 配置 
  * 配置目录服务（如果需要） 
  * 设置 VLAN
  * 配置代理服务器
  * 向群集网络添加 IP 地址
  * 存储加密证书

* [设置支持监视](#enable-support)

  必须接受配置工具的隐私政策，并且应该同时配置支持上传设置。

  群集可以自动上传有关群集的故障排除数据，包括统计信息和调试文件。 这些上传内容让 Microsoft 客户服务和支持部门得以提供最佳服务。 可以自定义受监视的内容，并可以选择启用主动支持和远程故障排除服务。  

### <a name="optional-configuration"></a>可选配置

并非所有群集都需要执行这些步骤。 某些类型的工作流程或特定群集管理样式需要这些步骤。 

* 自定义节点设置

  可以在群集范围级别设置节点名称和配置节点 IPMI 端口，也可单独进行设置和配置。 如果在将节点添加到群集之前配置这些设置，则新节点可以在加入时自动获取相关设置。 旧群集创建文档[自定义节点设置](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html)中介绍了这些选项。

  > [!TIP]
  > Microsoft Azure 文档站点上尚未提供此产品的某些文档。 [群集配置指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)和旧版[群集创建指南](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html)的链接将带你转到单独的 GitHub 托管网站。 

* 配置 SMB

  如果想要允许 SMB 访问群集以及 NFS，则必须配置 SMB 并将其打开。 SMB（有时称为 CIFS）通常用于支持 Microsoft Windows 客户端。

  针对 SMB 进行规划和配置涉及的不仅仅是单击控制面板中的几个按钮。 根据系统的要求，SMB 可以影响定义核心文件管理器的方式、创建的 vserver 数量、配置联接和命名空间的方式、访问权限以及其他设置。

  有关详细信息，请阅读“群集配置指南”的[配置 SMB 访问](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html)部分。

* 安装其他许可证

  如果想要使用 Azure Blob 之外的云存储，则必须安装其他功能许可证。 有关购买 FlashCloud<sup>TM</sup> 许可证的详细信息，请与 Microsoft 代表联系。 详细信息在[添加后端存储并配置虚拟命名空间](fxt-add-storage.md#about-back-end-storage)中进行说明。


### <a name="enable-support"></a>启用支持

Azure FXT Edge Filer 群集可以自动上传有关群集的支持数据。 这些上传内容让员工得以提供最佳的客户服务。

请按照以下步骤设置支持上传。

1. 导航到“群集” > “支持”设置页   。 接受隐私政策。 

   ![显示控制面板和弹出窗口（包含用于接受隐私政策的“确认”按钮）的屏幕截图](media/fxt-cluster-create/fxt-privacy-policy.png)

1. 单击“客户信息”左侧的三角形  以展开该部分。
1. 单击“重新验证上传信息”  按钮。
1. 在“唯一群集名称”  中设置群集的支持名称，确保它可以唯一地向支持人员标识你的群集。
1. 选择与“统计信息监视”  、“常规信息上传”  和“故障信息上传”  对应的复选框。
1. 单击“提交”  。  

   ![包含支持设置页的完整客户信息部分的屏幕截图](media/fxt-cluster-create/fxt-support-info.png)

1. 单击“安全主动支持 (SPS)”左侧的三角形  以展开该部分。
1. 单击“启用 SPS 链接”  的复选框。
1. 单击“提交”  。

   ![包含支持设置页上的完整“安全主动支持”部分的屏幕截图](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>后续步骤

创建基本群集并接受隐私政策后，添加其余群集节点。 

> [!div class="nextstepaction"]
> [添加群集节点](fxt-add-nodes.md)
