---
title: Azure 流量分析常见问题解答 | Microsoft Docs
description: 获取有关流量分析的常见问题的解答。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 3ab06b624d1e433641d190d9621592ef83df3344
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>流量分析常见问题解答

1.  使用流量分析的先决条件是什么？

    流量分析要求满足以下先决条件：

    - 支持网络观察程序的订阅
    - 为要监视的 NSG 启用 NSG 流日志
    - 一个 Azure 存储帐户，用于存储原始流日志
    - 具有读取和写入访问权限的 Log Analytics (OMS) 工作区
    - 帐户在 Microsoft.Network 提供程序上必须赋予以下操作：

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  可在哪些 Azure 区域使用流量分析？

    在预览版中，可对以下任何受支持区域中的 NSG 使用流量分析：美国中西部、美国东部、美国东部 2 区、美国中北部、美国中南部、美国中部、美国西部、美国西部 2 区、西欧、北欧、英国西部、英国南部、澳大利亚东部和澳大利亚东南部。 Log Analytics 工作区必须位于美国中西部、美国东部、西欧、澳大利亚东南部或英国南部区域。

3.  启用流日志的 NSG 是否可与 OMS 工作区位于不同的区域？

    是

4.  是否可以在单个工作区中配置多个 NSG？

    是

5.  是否可以使用现有的 OMS 工作区？

    可以。如果选择现有的工作区，请确保将此工作区迁移到新的查询语言。 如果不想要升级该工作区，则需要创建新的工作区。 有关新查询语言的详细信息，请参阅[将 Azure Log Analytics 升级到新的日志搜索](../log-analytics/log-analytics-log-search-upgrade.md)。

6.  是否可将 Azure 存储帐户放在一个订阅中，并将 OMS 工作区放在另一个订阅中？

    是

7.  是否可将原始日志存储在不同订阅的不同存储帐户中？

    不会。 可将原始日志存储在为 NSG 启用了流日志的任何存储帐户中，但是，该存储帐户和原始日志必须位于相同的订阅和区域中。

8.  如果在为流量分析配置 NSG 时收到“未找到”错误，如何解决该错误？

    请选择问题 2 中所列的某个受支持区域。 如果选择不支持的区域，则会收到“未找到”错误。

9.  NSG 流日志下显示 NSG 状态“无法加载”，接下来要怎样做？

    要使流日志记录正常工作，必须注册 Microsoft.Insights 提供程序。 如果不确定是否为订阅注册了 Microsoft.Insights 提供程序，请替换以下命令中的 *xxxxx-xxxxx-xxxxxx-xxxx*，然后从 PowerShell 运行以下命令：

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. 我已配置解决方案。 为何仪表板上未显示任何信息？

    首次显示仪表板最长可能需要花费 30 分钟。 解决方案必须先聚合足够的数据以派生有意义的见解，然后才能生成任何报告。 

11.  如果收到以下消息：“在选定的时间间隔内无法在此工作区中找到任何数据。 请尝试更改时间间隔，或选择其他工作区，”该如何解决？

        请尝试以下选项：
        - 尝试在上部菜单栏中更改时间间隔。
        - 在上部菜单栏中选择不同的 Log Analytics 工作区
        - 如果流量分析是最近才启用的，请尝试在 30 分钟后访问它
    
        如果问题仍未解决，请在 [User Voice 论坛](https://feedback.azure.com/forums/217313-networking?category_id=195844)中咨询。

12.  如果收到以下消息：“1) 正在首次分析 NSG 流日志。 此过程可能需要 20-30 分钟才能完成。 请过一段时间回来查看。 2) 如果上述步骤不起作用，并且工作区位于免费 SKU，请在此处检查工作区使用情况，以验证是否超出配额，或者参阅常见问题解答中的其他信息”，该如何解决？

        此错误可能由以下原因导致：
        - 流量分析可能是最近启用的，可能尚未聚合足够的数据，无法获得有意义的见解。
        - OMS 工作区位于免费 SKU，并违反了配额限制。 对于这种情况，可能需要使用容量更大的 SKU 中的工作区。
    
        如果问题仍未解决，请在 [User Voice 论坛](https://feedback.azure.com/forums/217313-networking?category_id=195844)中咨询。
    
13.  如果收到以下消息：“似乎我们已获得资源数据（拓扑），但没有流信息。 同时，请单击此处查看资源数据，并参阅常见问题解答了解其他信息”，该如何解决？

        仪表板上显示了资源信息，但未显示与流相关的统计信息。 不显示这些数据的原因是资源之间没有通信流。 请在 60 分钟后重新检查状态。 如果确信资源之间存在通信流，请在 [User Voice 论坛](https://feedback.azure.com/forums/217313-networking?category_id=195844)中咨询。

14. 是否可以使用 PowerShell 或 Azure 资源管理器模板配置流量分析？

    否，只能使用 Azure 门户配置流量分析。

15.  流量分析如何计费？

        流量分析按以下内容计费：增强精简的日志并将它们存储在 Log Analytics 工作区中。 在预览版中，不会因增强精简的日志而对流量分析计费，但工作区中的数据保留需按照发布的费率计费。 发布流量分析的定价后，将更新本答案。

16.  如何使用地图视图中的键盘导航？

        地图页面包含两个主要部分：
    
        - **标题**：使用地图顶部的标题可以通过“部署”、“无部署”、“活动”、“非活动”、“已启用流量分析”、“未启用流量分析”、“来自国家/地区的流量”、“良性”、“恶意”、“允许的恶意流量”等按钮和图例信息选择流量分布筛选器。 选择定义的按钮后，相应的筛选器将应用到地图。例如，如果某个用户选择了标题下面的“活动”筛选器按钮，则地图会突出显示部署中的“活动”数据中心。
        - **地图**：标题下面的“地图”部分显示不同 Azure 数据中心和国家/地区的流量分布。
    
        **标题中的键盘导航**
    
        - 默认情况下，可以使用“Azure DC”筛选器按钮在标题的地图页面中选择。
        - 若要导航到另一个筛选器按钮，可以使用 `Tab` 或 `Right arrow` 键转到下一个选项。 若要导航回来，可以使用 `Shift+Tab` 或 `Left arrow` 键。 向前导航方向的优先顺序是从左到右，然后是从上到下。
        - 按 `Enter` 或 `Down` 箭头键可应用选定的筛选器。 根据选择的筛选器和部署，将在“地图”部分下面突出显示一个或多个节点。
            - 若要在“标题”与“地图”之间切换，请按 `Ctrl+F6`。
        
        **地图中的键盘导航**
    
        - 在标题中选择任一筛选器并按 `Ctrl+F6` 后，焦点将转移到地图视图中某个突出显示的节点（“Azure 数据中心”或“国家/地区”）。
        - 若要导航到地图中突出显示的其他节点，可以使用 `Tab` 或 `Right arrow` 键向前移动，或者使用 `Shift+Tab` 或 `Left arrow` 键向后移动。
        - 若要在地图中选择突出显示的任一节点，可以使用 `Enter` 或 `Down arrow` 键。
        - 选择任一此类节点后，焦点会转移到节点的“信息工具框”。 默认情况下，焦点会转移到“信息工具框”中的关闭按钮。 若要进一步在“框”视图中导航，可分别使用 `Right` 和 `Left arrow` 向前和向后移动。 按 `Enter` 的效果与在“信息工具框”中选择聚焦的按钮相同。
        - 当焦点位于“信息工具框”时，如果按 `Tab`，则焦点会转移到选定节点所在的同一大洲中的终结点。 可以使用 `Right` 和 `Left arrow` 键来浏览这些终结点。
        - 若要导航到其他流终结点/大洲群集，请使用 `Tab` 向前移动，或使用 `Shift+Tab` 向后移动。
        - 焦点位于 `Continent clusters` 后，可以使用 `Enter` 或 `Down` 箭头键突出显示大洲群集中的终结点。 若要在大洲群集的信息框中浏览终结点和使用关闭按钮，可分别使用 `Right` 或 `Left arrow` 键向前或向后移动。 在任一终结点上，可以使用 `Shift+L` 切换到从选定节点到终结点的连接线。 再次按 `Shift+L` 可导航到选定的终结点。
        
        在任何阶段：
    
        - 按 `ESC` 可折叠已展开的选定内容。
        - 按 `UP Arrow` 键可执行按 `ESC` 时所执行的相同操作。 按 `Down arrow` 键可执行按 `Enter` 时所执行的相同操作。
        - 使用 `Shift+Plus` 可以放大，使用 `Shift+Minus` 可以缩小。
