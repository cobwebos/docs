---
title: 创建连接监视器预览-Azure 门户
titleSuffix: Azure Network Watcher
description: 了解如何使用 Azure 门户创建 (预览) 的连接监视器。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 4e7067e537ce8fb6faf82198f7863957f79ffb22
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567874"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>使用 Azure 门户 (预览) 创建连接监视器

了解如何使用 Azure 门户创建连接监视器 (预览) 来监视资源之间的通信。 它支持混合和 Azure 云部署。

## <a name="before-you-begin"></a>开始之前 

在连接监视器（预览版）中创建的连接监视器中，可以将本地计算机和 Azure VM 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

连接监视器（预览版）包含以下实体：


* **连接监视器资源** – 特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点** – 参与连接检查的源或目标。 终结点的示例包括 Azure VM、本地代理、URL 和 IP。
* **测试配置** – 针对测试的特定于协议的配置。 根据选定协议，可以定义端口、阈值、测试频率和其他参数。
* **测试组** – 包含源终结点、目标终结点和测试配置的组。 连接监视器可包含多个测试组。
* **测试** – 将源终结点、目标终结点和测试配置组合在一起。 测试是可用于监视数据的最精细级别。 监视数据包括检查失败的百分比和往返时间 (RTT)。

    ![显示连接监视器的示意图（该图定义了测试组和测试之间的关系）](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>创建步骤

若要使用 Azure 门户 (预览创建连接监视器) ，请执行以下步骤：

1. 在 Azure 门户主页上，转到“网络观察程序”。
1. 在左侧的“监视”部分，选择“连接监视器（预览版）” 。
1. 你会看到在连接监视器（预览版）中创建的所有连接监视器。 若要查看在经典连接监视器体验中创建的连接监视器，请转到“连接监视器”选项卡。

    ![显示连接监视器（预览版）中创建的连接监视器的屏幕截图](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. 在“连接监视器（预览版）”仪表板的左上角，选择“创建” 。
1. 在“基本信息”选项卡上，为连接监视器输入信息：
   * **连接监视器名称**–添加连接监视器的名称。 使用针对 Azure 资源的标准命名规则。
   * **订阅**–为连接监视器选择订阅。
   * **区域**–为连接监视器选择一个区域。 只能选择在此区域创建的源 VM。
   * **工作区配置** - 工作区包含监视数据。 可以使用自定义工作区或默认工作区。 
       * 若要使用默认工作区，请选择此复选框。 
       * 若要选择自定义工作区，请清除此复选框。 然后为自定义工作区选择订阅和区域。 
1. 在选项卡底部，选择“下一步：测试组”。

   ![显示连接监视器中“基本信息”选项卡的屏幕截图](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 在“测试组”选项卡上，选择“+ 测试组” 。 若要设置测试组，请参阅[在连接监视器中创建测试组](#create-test-groups-in-a-connection-monitor)。 
1. 在选项卡底部，选择“下一步：查看 + 创建”可查看连接监视器。

   ![显示“测试组”选项卡和窗格（可以在其中添加测试组详细信息）的屏幕截图](./media/connection-monitor-2-preview/create-tg.png)

1. 先在“查看 + 创建”选项卡上查看基本信息和测试组，再创建连接监视器。 如果需要编辑连接监视器：
   * 若要编辑基本详细信息，请选择铅笔图标。
   * 若要编辑测试组，请将其选中。

   > [!NOTE] 
   > “查看 + 创建”选项卡显示连接监视器预览阶段每月的成本。 目前，“当前成本”列显示无需付费。 连接监视器公开上市之后，此列才会显示每月费用。 
   > 
   > 在连接监视器预览阶段，仍需支付 Log Analytics 引入费用。

1. 准备好创建连接监视器后，在“查看 + 创建”选项卡的底部，选择“创建” 。

   ![连接监视器的屏幕截图（显示“查看 + 创建”选项卡）](./media/connection-monitor-2-preview/review-create-cm.png)

连接监视器（预览版）在后台创建连接监视器资源。

## <a name="create-test-groups-in-a-connection-monitor"></a>在连接监视器中创建测试组

每个连接监视器中的测试组都包括按网络参数进行测试的源和目标。 它们测试的是失败检查的百分比和基于测试配置的 RTT。

在 Azure 门户中，若要在连接监视器中创建测试组，请为以下字段指定值：

* **禁用测试组**–可以选择此字段，以对测试组指定的所有源和目标禁用监视。 默认情况下，此选择处于未选中状态。
* **名称**–命名你的测试组。
* **源**–如果已在 Azure vm 和本地计算机上安装代理，则可以将其指定为源。 若要为源安装代理，请参阅[安装监视代理](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)。
   * 若要选择 Azure 代理，请选择“Azure 代理”选项卡。此处只能看到绑定到创建连接监视器时指定的区域的 VM。 默认情况下，会将 VM 分组到它们所属的订阅中。 已折叠这些组。 
   
       可以从订阅级别向下钻取到层次结构中的其他级别：

      订阅 > 资源组 > VNET > 子网 > 具有代理的 VM    

      还可以更改“分组依据”字段的值，从任何其他级别启动树。 例如，如果按虚拟网络分组，则会看到层次结构（VNET > 子网 > 具有代理的 VM）中具有代理的 VM  。

      ![连接监视器的屏幕截图（显示“添加源”面板和“Azure 代理”选项卡）](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 若要选择本地代理，请选择 "**非 Azure 代理**" 选项卡。默认情况下，代理按区域分组到工作区中。 所有这些工作区都配置了网络性能监视器解决方案。 
   
       如果需要将网络性能监视器添加到工作区，请通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)获取。 有关如何添加网络性能监视器的信息，请参阅 [Azure Monitor 中的监视解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)。 
   
       在“创建连接监视器”视图的“基本信息”选项卡上，已选择默认区域 。 如果更改了区域，则可以在新区域的工作区中选择代理。 还可以更改“分组依据”字段的值，以按子网进行分组。

      ![连接监视器的屏幕截图（显示“添加源”面板和“非 Azure 代理”选项卡）](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 若要查看所选的 Azure 代理和非 Azure 代理，请转到“查看”选项卡。

      ![连接监视器的屏幕截图（显示“添加源”面板和“查看”选项卡）](./media/connection-monitor-2-preview/review-sources.png)

   * 设置好源之后，在“添加源”面板底部选择“完成” 。

* **目标**–可以通过将 Azure vm 或任何终结点的连接指定为目标来监视其连接性)  (公共 IP、URL 或 FQDN。 单个测试组中可以添加 Azure VM、Office 365 URL、Dynamics 365 URL 和自定义终结点。

    * 若要选择 Azure VM 作为目标，请选择“Azure VM”选项卡。默认情况下，Azure VM 会分组到同一区域中的订阅层次结构，该区域是在“基本信息”选项卡上的“创建连接监视器”视图中选择的 。可以更改区域，并从新选择的区域中选择“Azure VM”。 然后可以从订阅级别向下钻取到层次结构中的其他级别，比如 Azure 代理级别。

       ![“添加目标”窗格的屏幕截图（显示“Azure VM”选项卡）](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![“添加目标”窗格的屏幕截图（显示订阅级别）](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 若要选择终结点作为目标，请选择“终结点”选项卡。终结点列表包含 Office 365 测试 URL 和 Dynamics 365 测试 URL（按名称分组）。 除了这些终结点外，还可以选择在同一连接监视器中其他测试组中创建的终结点。 
    
        若要添加新的终结点，请在右上角选择“+ 终结点”。 然后提供终结点名称和 URL、IP 或 FQDN。

       ![显示在连接监视器中将终结点作为目标添加到何处的屏幕截图](./media/connection-monitor-2-preview/add-endpoints.png)

    * 若要查看所选的 Azure VM 和终结点，请选择“查看”选项卡。
    * 选择完目标后，选择“完成”。

* **测试配置**–您可以关联测试组中的测试配置。 Azure 门户仅允许每个测试组有一个测试配置，但可以使用 ARMClient 添加更多测试。

    * **名称**–命名测试配置。
    * **协议**–选择 "TCP"、"ICMP" 或 "HTTP"。 若要将 HTTP 更改为 HTTPS，请选择“HTTP”作为协议，并选择“443”作为端口 。
        * "**创建网络测试配置**"-仅当在 "**协议**" 字段中选择 " **HTTP** " 时才会显示此复选框。 选择此框以创建另一个测试配置，该配置使用你在配置中其他地方指定的相同源和目标。 新创建的测试配置名为 `<the name of your test configuration>_networkTestConfig`。
        * **禁用 traceroute** –此字段适用于其协议为 TCP 或 ICMP 的测试组。 选择此框以阻止源发现拓扑和逐跳 RTT。
    * **目标端口**–您可以使用所选的目标端口自定义此字段。
    * **测试频率**–使用此字段可选择源对指定的协议和端口 ping 目标的频率。 可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 源将根据所选的值来测试与目标的连接。  例如，如果选择 30 秒，则源将在 30 秒的时间段内至少检查一次与目标的连接。
    * **成功阈值**–你可以在以下网络参数上设置阈值：
       * **检查失败**–设置在源使用指定的条件检查与目标的连接时可能失败的检查的百分比。 对于 TCP 或 ICMP 协议，检查失败的百分比可能会与数据包丢失的百分比相同。 对于 HTTP 协议，此字段表示未接收到响应的 HTTP 请求的百分比。
       * **往返时间**–设置通过测试配置连接到目标所需的源时间长度（以毫秒为单位）。
    
       ![显示在连接监视器中设置测试配置的位置的屏幕截图](./media/connection-monitor-2-preview/add-test-config.png)


## <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试配置数：2通过 Azure 门户

## <a name="next-steps"></a>后续步骤

* 了解[如何分析监视数据并设置警报](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* 了解[如何诊断网络中的问题](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
