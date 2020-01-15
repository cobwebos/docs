---
title: 教程：将存储添加到 Azure FXT Edge Filer 群集
description: 如何为 Azure FXT Edge Filer 配置后端存储和面向客户端的伪命名空间
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551958"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>教程：添加后端存储并配置虚拟命名空间 

本教程介绍如何为缓存添加后端存储，以及如何设置面向客户端的虚拟文件系统。 

群集连接到后端存储系统以访问数据客户端请求并将更改永久存储在缓存中。 

名称空间是面向客户端的伪文件系统，让你可以交换后端存储，而不更改客户端工作流。 

本教程的内容： 

> [!div class="checklist"]
> * 如何将后端存储添加到 Azure FXT Edge Filer 群集 
> * 如何定义面向客户端的存储路径

## <a name="about-back-end-storage"></a>关于后端存储

Azure FXT Edge Filer 群集使用“核心文件管理器”定义，将后端存储系统链接到 FXT 群集  。

Azure FXT Edge Filer 与几种常用的 NAS 硬件系统兼容，可以使用来自 Azure Blob 或其他云存储的空容器。 

云存储容器在添加时必须为空，以便 FXT 操作系统可以完全管理云存储卷上的所有数据。 在将容器作为核心文件管理器添加到群集之后，可以将现有数据移动到云容器中。

使用控制面板向系统添加核心文件管理器。

> [!NOTE]
> 
> 如果要使用 Amazon AWS 或 Google 云存储，则必须安装 FlashCloud<sup>TM</sup> 功能许可证。 请联系 Microsoft 代表以获取许可证密钥，然后按照旧版配置指南中有关[添加或删除功能许可证](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)的说明进行操作。
> 
> Azure Blob 存储的支持包含在 Azure FXT Edge Filer 软件许可证中。 

有关添加核心文件管理器的详细信息，请阅读《群集配置指南》的以下部分：

* 有关选择和准备添加核心文件管理器的更多信息，请阅读[使用核心文件管理器](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview)。
* 有关详细的先决条件和分步说明，请阅读以下文章：

  * [添加新的 NAS 核心文件管理器](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [添加新的云核心文件管理器](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

添加核心文件管理器后，可以在“核心文件管理器详细信息设置”页上更新其设置。

## <a name="add-a-core-filer"></a>添加核心文件管理器

单击“核心文件管理器” > “管理核心文件管理器”设置页面上的“创建”按钮，定义核心文件管理器    。

![单击“管理核心文件管理器”页面上核心文件管理器列表上方的“创建”按钮](media/fxt-cluster-config/create-core-filer-button.png)

“添加新的核心文件管理器”向导将引导你完成创建链接到后端存储的核心文件管理器  。 《群集配置指南》分步说明了该过程，NFS/NAS 存储和云存储的过程描述并不相同（链接在上面）。 

子任务包括：

* 指定核心文件管理器（NAS 或云）的类型

  ![“硬件 NAS 新的核心文件管理器”向导的第一页。 禁用“云核心文件管理器”选项，并显示有关丢失许可证的错误消息。](media/fxt-cluster-config/new-nas-1.png)

* 设置核心文件服务器的名称。 选择一个名称，帮助群集管理员理解它代表的存储系统。

* 对于 NAS 核心文件管理器，请提供完全限定的域名 (FQDN) 或 IP 地址。 建议所有核心文件管理器使用 FQDN，而 SMB 访问则必须使用 FQDN。

* 选择缓存策略 - 向导的第二页列出了新的核心文件管理器的可用缓存策略。 有关详细信息，请阅读[《群集配置指南》的缓存策略部分](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)。 

  ![“硬件 NAS 新的核心文件管理器”向导的第二页；“缓存策略”下拉菜单已打开，显示几个禁用的选项和三个有效的缓存策略选项（跳过、读取缓存和读/写缓存）。](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* 对于云存储，除了其他参数，还必须指定云服务和访问凭据。 有关详细信息，请阅读《群集配置指南》中的[云服务和协议](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol)。

  ![“新的核心文件管理器”向导中的云核心文件管理器信息](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  如果已经为此群集添加了云访问凭据，则它们将在列表中显示。 在“群集” > “云凭据”设置页中更新并添加凭据   。 

在向导中填写所有必需设置后，单击“添加文件管理器”按钮以提交更改  。

几分钟后，存储系统会显示在“仪表板”的核心文件管理器列表上，可以通过核心文件管理器设置页访问  。

![“管理核心文件管理器”设置页中的核心文件管理器“Flurry-NAS”，文件管理器详细信息视图已展开](media/fxt-cluster-config/core-filer-in-manage-page.png)

此屏幕截图中的核心文件管理器缺少 VServer。 必须将核心文件管理器链接到 VServer，并创建一个交接点，以便客户端可以访问存储。 下面的[配置命名空间](#configure-the-namespace)中描述了这些步骤。

## <a name="configure-the-namespace"></a>配置命名空间

Azure FXT Edge Filer 群集创建名为“群集命名空间”的虚拟文件系统，简化了客户端对不同后端系统上存储的数据的访问  。 由于客户端使用虚拟路径请求文件，因此可以添加或替换存储系统，而无需更改客户端工作流。 

通过群集命名空间还能以类似的文件结构表示云和 NAS 存储系统。 

群集的 VServer 维护命名空间并向客户端提供内容。 创建群集命名空间有两个步骤： 

1. 创建 VServer 
1. 在后端存储系统和面向客户端的文件系统路径之间设置交接点 

### <a name="create-a-vserver"></a>创建 VServer

VServer 是虚拟文件服务器，用于控制客户端和群集的核心文件管理器之间的数据流动方式：

* VServer 托管面向客户端的 IP 地址
* VServer 创建命名空间，并定义将面向客户端的虚拟目录结构映射到后端存储上的导出的交接点
* VServer 强制执行文件访问控制，包括核心文件管理器导出策略和用户身份验证系统
* VServer 提供 SMB 基础结构

在开始配置群集 VServer 之前，请阅读链接文档并咨询 Microsoft 代表，以获取理解命名空间和 VServer 的帮助。 如果使用 VLAN，则在创建 VServer 之前[创建 VLAN](fxt-configure-network.md#adjust-network-settings)。 

《群集配置指南》的相关部分将帮助你熟悉 FXT VServer 和全局命名空间功能：

* [创建和使用 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [使用全局命名空间](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [创建 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

群集至少需要一个 VServer。 

若要创建新的 VServer，需要以下信息：

* 为 VServer 设置的名称

* VServer 将处理的面向客户端的 IP 地址范围

  创建 VServer 时，必须提供一个连续 IP 地址范围。 之后可以使用“面向客户端的网络”设置页来添加更多地址  。

* 如果网络具有多个 VLAN，则此 VServer 使用哪个 VLAN

使用“VServer” > “管理 VServer”设置页创建新的 VServer   。 有关更多详细信息，请阅读《群集配置指南》中的[创建 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)。 

![用于创建新 VServer 的弹出窗口](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>创建交接点

“交接点”将后端存储路径映射到客户端可见的命名空间  。

因为一条虚拟路径可以容纳来自多个核心文件管理器的存储，所以可以使用此系统简化客户端装入点中使用的路径，并无缝缩放容量。

![添加已填充设置的“新建交接点”向导页面](media/fxt-cluster-config/add-junction-full.png)

有关创建命名空间交接点的完整详细信息，请参考《群集配置指南》中的 [VServer > 命名空间](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)   。

![显示交接点详情的“Vserver”>“命名空间”设置页](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>配置导出规则

在同时具有 VServer 和核心文件管理器之后，应该自定义导出规则和导出策略，用以控制客户端如何访问核心文件管理器导出上的文件。

首先，使用“VServer” > “导出规则”页面添加新规则、修改默认策略或者创建自己的自定义导出策略   。

然后，使用“VServer” > “导出策略”页面，在通过该 VServer 进行访问时将自定义策略应用于核心文件管理器的导出   。

有关详细信息，请阅读《群集配置指南》文章中的[控制对核心文件管理器导出的访问](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html)。


## <a name="next-steps"></a>后续步骤

添加存储并配置面向客户端的命名空间之后，完成群集的初始设置： 

> [!div class="nextstepaction"]
> [配置群集的网络设置](fxt-configure-network.md)
