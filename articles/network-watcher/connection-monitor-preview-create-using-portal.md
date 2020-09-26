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
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362873"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>使用 Azure 门户 (预览) 创建连接监视器

了解如何使用 Azure 门户创建连接监视器 (预览) 来监视资源之间的通信。 它支持混合和 Azure 云部署。

## <a name="before-you-begin"></a>开始之前 

在连接监视器（预览版）中创建的连接监视器中，可以将本地计算机和 Azure VM 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

连接监视器（预览版）包含以下实体：


* **连接监视器资源** – 特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点** – 参与连接检查的源或目标。 终结点的示例包括 
    * Azure VM
    * Azure Vnet
    * Azure 子网
    * 本地代理
    * 本地子网
    * 由多个子网组成的本地自定义网络
    * Url 和 Ip 
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

   ![显示连接监视器中“基本信息”选项卡的屏幕截图](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 在“基本信息”选项卡上，为连接监视器输入信息：
   * **连接监视器名称** –添加连接监视器的名称。 使用针对 Azure 资源的标准命名规则。
   * **订阅** –为连接监视器选择订阅。
   * **区域** –为连接监视器选择一个区域。 只能选择在此区域创建的源 VM。
   * **工作区配置** - 工作区包含监视数据。 可以使用自定义工作区或默认工作区。 
       * 若要使用默认工作区，请选择此复选框。 
       * 若要选择自定义工作区，请清除此复选框。 然后为自定义工作区选择订阅和区域。 
1. 在选项卡底部，选择“下一步：测试组”。

   ![显示连接监视器中的 "创建测试组" 选项卡的屏幕截图](./media/connection-monitor-2-preview/create-tg.png)

1. 在测试组中添加源、目标和测试配置。 若要设置测试组，请参阅[在连接监视器中创建测试组](#create-test-groups-in-a-connection-monitor)。 
1. 在选项卡底部，选择 " **下一步：创建警报**"。

   ![显示要在其中创建警报的窗格的屏幕截图](./media/connection-monitor-2-preview/create-alert.png)

1. 若要创建警报，请参阅 [在连接监视器中创建警报](#create-alerts-in-connection-monitor)
1. 在选项卡底部，选择 " **下一步：审核 + 创建**"。

  ![连接监视器的屏幕截图（显示“查看 + 创建”选项卡）](./media/connection-monitor-2-preview/review-create-cm.png)

1. 先在“查看 + 创建”选项卡上查看基本信息和测试组，再创建连接监视器。 如果需要编辑连接监视器：
   * 若要编辑基本详细信息，请选择铅笔图标。
   * 若要编辑测试组，请将其选中。

   > [!NOTE] 
   > “查看 + 创建”选项卡显示连接监视器预览阶段每月的成本。 目前，“当前成本”列显示无需付费。 连接监视器公开上市之后，此列才会显示每月费用。 
   > 
   > 在连接监视器预览阶段，仍需支付 Log Analytics 引入费用。

1. 准备好创建连接监视器后，在“查看 + 创建”选项卡的底部，选择“创建” 。

连接监视器（预览版）在后台创建连接监视器资源。

## <a name="create-test-groups-in-a-connection-monitor"></a>在连接监视器中创建测试组

每个连接监视器中的测试组都包括按网络参数进行测试的源和目标。 它们测试的是失败检查的百分比和基于测试配置的 RTT。

在 Azure 门户中，若要在连接监视器中创建测试组，请为以下字段指定值：

* **禁用测试组** –可以选择此字段，以对测试组指定的所有源和目标禁用监视。 默认情况下，此选择处于未选中状态。
* **名称** –命名你的测试组。
* **源** –如果已在 Azure vm 和本地计算机上安装代理，则可以将其指定为源。 若要为源安装代理，请参阅[安装监视代理](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)。
   * 若要选择 Azure 代理，请选择 " **azure 终结点** " 选项卡。在这里，只能看到绑定到创建连接监视器时指定的区域的 Vm。 默认情况下，会将 VM 分组到它们所属的订阅中。 已折叠这些组。 
   
       可以从订阅级别向下钻取到层次结构中的其他级别：

      订阅 > 资源组 > VNET > 子网 > 具有代理的 VM    

      还可以更改 " **分组依据** " 字段的值，以从任何其他级别启动树。 例如，如果按虚拟网络分组，则会看到层次结构（VNET > 子网 > 具有代理的 VM）中具有代理的 VM  。
       选择 VNET、子网或单个 VM 会将相应的资源 ID 设置为终结点。 默认情况下，具有网络观察程序扩展的选定 VNET 或子网中的所有 Vm 都参与监视。 若要减小范围，请选择 "特定子网/代理" 或更改 "范围" 属性的值。 

      ![连接监视器的屏幕截图（显示“添加源”面板和“Azure 代理”选项卡）](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 若要选择本地代理，请选择 " **非 Azure 代理** " 选项卡。默认情况下，代理按区域分组到工作区中。 所有这些工作区都配置了网络性能监视器解决方案。 
   
       如果需要将网络性能监视器添加到工作区，请通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)获取。 有关如何添加网络性能监视器的信息，请参阅 [Azure Monitor 中的监视解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)。 
   
       在“创建连接监视器”视图的“基本信息”选项卡上，已选择默认区域 。 如果更改了区域，则可以在新区域的工作区中选择代理。  你可以选择一个或多个代理或子网。 在 "子网" 视图中，可以选择要监视的特定 Ip。 添加多个子网将创建一个自定义的本地网络，即 "OnPremises_Network_1"。 还可以更改“分组依据”字段的值，以按子网进行分组。

      ![连接监视器的屏幕截图（显示“添加源”面板和“非 Azure 代理”选项卡）](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * 你还可以使用 "最近使用的 **终结点** " 选项卡选择最近使用的终结点 
   
   * 完成源设置后，请选择 " **完成**"。 你仍可以通过在 "创建测试组" 视图中单击终结点来编辑基本属性（如终结点名称）。 

* **目标** –可以通过将 Azure vm、本地计算机或任何终结点指定为目标来监视其与 (公共 IP、URL 或 FQDN) 的连接。 在单个测试组中，可以添加 Azure Vm、本地计算机、Office 365 Url、Dynamics 365 Url 和自定义终结点。

    * 若要选择 Azure Vm 作为目标，请选择 " **azure 终结点** " 选项卡。默认情况下，Azure Vm 将分组到你在 " **创建连接监视器** " 视图中选择的同一区域中的 " **基本** 信息" 选项卡上。可以更改区域，并从新选择的区域中选择 "Azure Vm"。 然后，你可以向下钻取层次结构中的其他级别，就像源 Azure 终结点一样。
     可以选择 Vnet、子网或单个 Vm，类似于源 Azure 终结点。选择 VNET、子网或单个 VM 会将相应的资源 ID 设置为终结点。 默认情况下，具有网络观察程序扩展的选定 VNET 或子网中的所有 Vm 都参与监视。 若要减小范围，请选择 "特定子网/代理" 或更改 "范围" 属性的值。 

       ![“添加目标”窗格的屏幕截图（显示“Azure VM”选项卡）](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![“添加目标”窗格的屏幕截图（显示订阅级别）](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * 若要选择非 Azure 代理作为目标，请选择 "非 Azure 终结点" 选项卡。默认情况下，代理按区域分组到工作区中。 所有这些工作区都配置了网络性能监视器解决方案。 如果需要将网络性能监视器添加到工作区，请通过 Azure 市场获取。 有关如何添加网络性能监视器的信息，请参阅 Azure Monitor 中的监视解决方案。 在 "创建连接监视器" 视图的 "基本信息" 选项卡上，选中 "默认区域"。 如果更改了区域，则可以在新区域的工作区中选择代理。 你可以选择一个或多个代理或子网。 在 "子网" 视图中，可以选择要监视的特定 Ip。 添加多个子网将创建一个自定义的本地网络，即 "OnPremises_Network_1"。  
    
     !["添加非 Azure 目标" 窗格的屏幕截图](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * 若要选择公共终结点作为目标，请选择 " **外部地址** " 选项卡。终结点列表包含 Office 365 测试 Url 和 Dynamics 365 测试 Url，按名称分组。 除了这些终结点外，还可以选择在同一连接监视器中其他测试组中创建的终结点。 
    
        若要添加新的终结点，请在右上角选择“+ 终结点”。 然后提供终结点名称和 URL、IP 或 FQDN。

       ![显示将公共终结点添加为连接监视器中的目标的位置的屏幕截图](./media/connection-monitor-2-preview/add-endpoints.png)

    * 若要选择最近使用的终结点，请访问 " **最近的终结点**"   选项卡。
    * 选择完目标后，选择“完成”。 你仍可以通过在 "创建测试组" 视图中单击终结点来编辑基本属性（如终结点名称）。 

* **测试配置** -你可以关联测试组中的一个或多个测试配置。 使用 "新配置" 选项卡创建新的测试配置，或使用同一连接监视器中通过 "选择现有" 选项卡使用的测试配置。

    * **名称** –命名测试配置。
    * **协议** –选择 "TCP"、"ICMP" 或 "HTTP"。 若要将 HTTP 更改为 HTTPS，请选择“HTTP”作为协议，并选择“443”作为端口 。
        * "**创建网络测试配置**"-仅当在 "**协议**" 字段中选择 " **HTTP** " 时才会显示此复选框。 选择此框以创建另一个测试配置，该配置使用你在配置中其他地方指定的相同源和目标。 新创建的测试配置名为 `<the name of your test configuration>_networkTestConfig`。
        * **禁用 traceroute** -当协议为 TCP 或 ICMP 时，此字段适用。 选择此框以阻止源发现拓扑和逐跳 RTT。
    * **目标端口** –您可以使用所选的目标端口自定义此字段。
        * 侦听端口-当协议为 TCP 时，此字段适用。 选择此框以打开选择的 TCP 端口（如果尚未打开）。 
    * **测试频率** –使用此字段可选择源对指定的协议和端口 ping 目标的频率。 可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 选择 "自定义" 以输入你选择的频率，在30秒到30分钟之间。 源将根据所选的值来测试与目标的连接。  例如，如果选择 30 秒，则源将在 30 秒的时间段内至少检查一次与目标的连接。
    * **成功阈值** –你可以在以下网络参数上设置阈值：
       * **检查失败** –设置在源使用指定的条件检查与目标的连接时可能失败的检查的百分比。 对于 TCP 或 ICMP 协议，检查失败的百分比可能会与数据包丢失的百分比相同。 对于 HTTP 协议，此字段表示未接收到响应的 HTTP 请求的百分比。
       * **往返时间** –设置通过测试配置连接到目标所需的源时间长度（以毫秒为单位）。
    
       ![显示在连接监视器中设置测试配置的位置的屏幕截图](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>在连接监视器中创建警报

您可以根据测试配置中设置的阈值在失败的测试上设置警报。   

在 Azure 门户中，若要在连接监视器中创建警报，请为以下字段指定值： 

- 创建警报-可以选择此字段，在 Azure Monitor 中创建指标警报。 选择此字段将启用其他字段进行编辑。 根据[警报的定价](https://azure.microsoft.com/pricing/details/monitor/)，还可以查看警报的额外费用 

- 作用域-> 资源和作用域 > 层次结构-根据 "基本信息" 选项卡中指定的值预填充此值 

- 条件-> 在 "测试结果 (preview) " 指标上创建警报。 连接监视器的测试结果失败时，将触发警报规则。 

- 操作组-您可以选择直接输入您的电子邮件，也可以选择通过操作组创建警报。 如果选择直接输入电子邮件，则会创建一个名为 "NPM Email 操作组" 的操作组，并将电子邮件 ID 添加到该操作组。如果选择使用 "操作组"，则必须选择以前创建的操作组。 可在此处了解如何创建操作组。成功创建警报后，你可以使用 "管理警报" 链接来管理你的警报。 

- 警报规则名称-连接监视器的名称 

- 创建时启用规则-这将根据条件启用警报规则。 如果要创建规则但不启用它，请禁用此规则。 

    ![显示 "在连接监视器中创建警报" 窗格的屏幕截图](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试配置数：2通过 Azure 门户

## <a name="next-steps"></a>后续步骤

* 了解 [如何分析监视数据并设置警报](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* 了解 [如何诊断网络中的问题](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
