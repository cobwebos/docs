---
title: 教程：将节点添加到 Azure FXT Edge Filer 群集
description: 如何添加节点到 Azure FXT Edge Filer 缓存存储
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6251fe8f88b7db25e3c09898540e07754d72fb0d
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551941"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>教程：将群集节点添加到 Azure FXT Edge Filer 群集

仅使用一个节点创建新的 Azure FXT Edge Filer 群集。 在进行其他配置之前，应至少再添加两个节点并启用高可用性。 

本教程介绍如何添加群集节点并启用高可用性（HA）功能。 

本教程介绍： 

> [!div class="checklist"]
> * 如何将节点添加到 FXT 群集
> * 如何启用 HA

本教程中的步骤大约需要 45 分钟才能完成。

在开始本教程之前，请打开要添加的节点并[设置其初始密码](fxt-node-password.md)。 

## <a name="1-load-the-cluster-nodes-page"></a>1.加载“群集节点”页面

在 Web 浏览器中打开群集的控制面板，然后以管理员身份登录。 （详细说明位于概述文章的[打开设置页面](fxt-cluster-create.md#open-the-settings-pages)下。）

控制面板在打开时显示“仪表板”选项卡  。 

![控制面板仪表板（第一个选项卡）](media/fxt-cluster-config/dashboard-1-node.png)

此图显示了新创建的具有单个节点的群集的仪表板。

## <a name="2-locate-the-node-to-add"></a>2.找到要添加的节点

若要添加节点，请单击“设置”选项卡，在“群集”部分选择“FXT 节点”页面    。

![控制面板“设置”选项卡（第二个选项卡），其中已加载“群集”>“FXT 节点”](media/fxt-cluster-config/settings-fxt-nodes.png)

“FXT 节点 - 未连接”列表显示了所有未分配的 FXT 节点（大多数数据中心只有几个这样的节点）  。 找到想要添加到群集的 FXT 节点。

> [!Tip] 
> 如果在“未加入”列表中找不到所需的节点，请检查它是否满足以下要求  ：
> 
> * 它已启动并已设置 [root 密码](fxt-node-password.md)。
> * 它已连接到你可以访问的网络。 如果使用 VLAN，则它必须与群集位于同一 VLAN 中。
> * 可以使用 Bonjour 协议检测到它。 
>
>   某些防火墙设置会阻止 Bonjour 使用的 TCP/UDP 端口，从而阻止 FXT 操作系统自动检测节点。
> 
> 如果要添加的节点不在列表中，请尝试以下解决方案： 
> 
> * 单击“手动发现”按钮，按 IP 地址查找它  。
> 
> * 手动分配临时 IP 地址。 这种情况很少见，但如果使用标记的 VLAN 并且节点不在正确的网络上，或者网络不允许自我分配的 IP 地址，则可能需要这样做。 按照本文档旧版本中的说明[手动设置静态 IP 地址](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)。

节点名称、IP 地址、软件版本和资格状态显示在列表中。 通常，“状态”列会显示“要加入”或描述导致节点无法加入群集的系统或硬件问题  。

“操作”列包含一些按钮，可用于将节点添加到群集或更新其软件  。 更新按钮会自动安装与群集中已有节点匹配的软件版本。

群集中的所有节点都必须使用相同版本的操作系统，但在添加节点之前无需更新软件。 单击“允许加入”按钮后，群集加入过程将自动检查并安装与群集上的版本匹配的操作系统软件  。

若要详细了解此页面上的选项，请参阅群集配置指南中的[“群集” > “FXT 节点”](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html)   。

## <a name="3-click-the-allow-to-join-button"></a>3.单击“允许加入”按钮 

单击要添加的节点的“操作”列中的“允许加入”按钮   。

单击该按钮后，节点的软件可能为准备将其添加到群集中而更新，其状态也会随之更新。 

下图显示了正在加入群集（最有可能的是在加入前进行操作系统更新）的节点。 对于正在添加到群集的节点，“操作”列中不显示任何按钮  。

![节点表的一行，显示了节点的名称、IP 地址、软件版本、“允许加入”消息和空白的最后一列](media/fxt-cluster-config/node-join-in-process.png)

几分钟后，新节点应出现在 FXT 节点设置页面顶部的群集节点列表中  。 

重复此过程以将其他节点添加到群集。 无需等待一个节点完成加入群集的过程，即可开始加入下一个。

## <a name="enable-high-availability"></a>启用高可用性

将第二个节点添加到群集后，可能会在控制面板仪表板上看到一条警告消息，指出未配置高可用性功能。 

高可用性（HA）允许群集节点在发生故障时相互补偿。 默认情况下不启用 HA。

![“仪表板”选项卡，“条件”表中显示了“群集有多个节点，但未启用 HA…”消息。](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> 群集中的节点少于三个时不要启用 HA。

按照此过程打开 HA： 

1. 在“设置”选项卡的“群集”部分，加载“高可用性”页面    。

   ![HA 配置页面（“群集”>“高可用性”）。 “启用 HA”复选框在顶部，提交按钮在底部。](media/fxt-cluster-config/enable-ha.png)

2. 单击标记为“启用 HA”的框，然后单击“提交”按钮   。 

“仪表板”上将显示一条警报，确认已启用 HA  。

![仪表板上会显示一条消息，“HA 已完全配置”](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>后续步骤

添加群集中的所有节点后，配置群集的长期存储以继续设置。

> [!div class="nextstepaction"]
> [添加后端存储并设置虚拟命名空间](fxt-add-storage.md)