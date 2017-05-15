---
title: "Azure 安全中心的 Windows VM 安全性 | Microsoft Docs"
description: "教程 - Azure 安全中心的 VM 安全性"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: b6fc4e710de740caf78dd1dcebc5432824add06c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>监视 Azure 安全中心的 VM 安全性

Azure 安全中心可帮助你深入了解与安全措施相关的 Azure 资源配置。 它还提供集成的安全性监视，可以检测到可能被忽略的威胁。 本教程提供 Azure 安全中心的简要概述，并说明如何将其用于 Azure 虚拟机。   

## <a name="security-center-overview"></a>安全中心概述

Azure 安全中心可帮助识别潜在的 VM 配置问题和目标安全威胁。 例如识别 VM 的以下问题：缺少网络安全组、磁盘未加密以及 RDP 暴力攻击。 Azure 安全中心仪表板上易于阅读的图中提供了此信息。

若要访问 Azure 安全中心仪表板，可单击 Azure 门户左侧导航窗格中的“安全中心”。 仪表板提供资源运行状况、安全警报和配置建议的高级视图。 在此处可以查看 Azure 环境的安全性状况、查找当前建议的计数以及查看威胁警报的当前状态。 以上每种高级图表均可以展开，其中提供关注区域的更多详细信息。

![ASC 仪表板](./media/tutorial-azure-security/asc-dash.png)

Azure 安全中心可针对检测到的问题提供建议，功能远不止数据发现。 例如，如果部署 VM 时没有附加网络安全组，则将生成包括修正步骤的建议。 这些建议还提供修正自动执行，无需离开 Azure 安全中心环境。  

![建议](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>配置数据收集

若要深入了解 VM 安全配置，首先需要配置 Azure 安全中心数据收集。 此过程涉及启用数据收集和创建用于保存收集数据的 Azure 存储帐户。 

1. 在 Azure 安全中心仪表板中，单击“安全策略”，然后选择你的订阅。 
2. 在“数据收集”下选择“打开”。
3. 单击“选择存储帐户”，然后新建存储帐户。 完成后选择“确定”。
4. 单击“安全策略”边栏选项卡上的“保存”。 

完成此操作后，所有虚拟机上都将安装 Azure 安全中心数据收集代理，数据收集开始。 

## <a name="configure-security-policy"></a>配置安全策略

安全策略定义安全策略项，针对这些项进行数据收集和提出建议。 默认情况下，将针对所有策略项评估 Azure 资源。 可为所有 Azure 资源全局禁用各个策略项或按资源组进行禁用。 此配置可将不同的安全策略应用到不同的 Azure 资源集。 有关 Azure 安全中心安全策略的详细信息，请参阅[在 Azure 安全中心中设置安全策略](../../security-center/security-center-policies.md)。 

为所有 Azure 资源配置安全策略：

1. 在 Azure 安全中心仪表板中，单击“安全策略”，然后选择你的订阅。 
2. 单击“保护策略”。
3. 启用或禁用需要应用于所有 Azure 资源的策略项。
4. 完成后单击“确定”。
5. 单击“安全策略”边栏选项卡上的“保存”。 

若要为特定资源组配置策略，请执行相同的步骤，但不要在“安全策略”边栏选项卡上选择订阅，而是选择资源组。 配置策略时，选择“继承”下的“唯一”。 如果要禁用特定资源组的数据收集，也可以在此处进行此配置。

在下面的示例中，已为名为“myResoureGroup”的资源组创建唯一策略。 在此策略中，磁盘加密和 Web 应用程序防火墙建议均已禁用。

![唯一策略](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>查看 VM 配置运行状况

启用数据收集并配置安全策略后，Azure 安全中心将开始提供警报和建议。 部署 VM 时，系统将安装数据收集代理并将这些新 VM 的数据填入 Azure 安全中心。 有关 VM 配置运行状况的详细信息，请参阅[保护 Azure 安全中心中的虚拟机](../../security-center/security-center-virtual-machine-recommendations.md)。 

收集数据时，每个 VM 和相关 Azure 资源的资源运行状况将聚合，并以易于读取的图表形式显示。 若要查看资源运行状况，请返回 Azure 安全中心仪表板。 在“资源安全性运行状况”下，单击“计算”。 最后，单击“计算”边栏选项卡上的“虚拟机”。 此视图提供所有 VM 配置状态的摘要。

![计算运行状况](./media/tutorial-azure-security/compute-health.png)

选择一个 VM 将显示针对该 VM 的所有建议。 本教程的下一节中将详细介绍建议。

## <a name="remediate-configuration-issues"></a>修正配置问题

配置数据开始填入 Azure 安全中心后，系统将针对配置的安全策略提供建议。 例如，如果配置 VM 时未关联网络安全组，将生成建议创建网络安全组的建议。 查看所有建议列表： 

1. 在 Azure 安全中心仪表板中，单击“建议”。
3. 选择特定建议，随后将打开包含建议针对的所有资源的列表的边栏选项卡。
4. 选择要处理的特定资源。
5. 按照屏幕上的修正步骤说明进行操作。 

在许多情况下，Azure 安全中心可提供解决建议的可操作步骤，并且无需离开 Azure 安全中心环境。 例如，在下面的示例中，系统检测到 NSG 的入站规则没有限制。 在此建议中，可以选择“编辑入站规则”按钮，这可提供修改规则所需的恰当 UI。 

![建议](./media/tutorial-azure-security/remediation.png)

建议修正后将标记为已解决。 

## <a name="view-detected-threats"></a>查看检测到的威胁

除了资源配置建议外，Azure 安全中心还提供威胁检测警报。 安全警报功能聚合从每个 VM、Azure 网络日志和连接的合作伙伴解决方案中收集的数据，以便检测针对 Azure 资源的安全威胁。 有关 Azure 安全中心威胁检测功能的详细信息，请参阅 [Azure 安全中心检测功能](../../security-center/security-center-detection-capabilities.md)。

此安全警报功能要求 Azure 安全中心定价层从“免费”*提升到“标准”*。 执行此操作时，可使用 30 天免费试用版。 更改定价层：  

1. 在 Azure 安全中心仪表板中，单击“安全策略”，然后选择你的订阅。
2. 单击“定价层”。
3. 选择新层，然后单击“选择”。
5. 单击“安全策略”边栏选项卡上的“保存”。 

启用后，安全警报图表将在检测到安全威胁时开始填充。

![安全警报](./media/tutorial-azure-security/security-alerts.png)

选择警报以查看信息，如威胁的描述、检测时间、威胁意图和建议的修正措施。 在此示例中，检测到 RDP 暴力攻击，294 次 RDP 尝试失败，并提供了建议的解决方法。

![RDP 攻击](./media/tutorial-azure-security/rdp-attack.png)
