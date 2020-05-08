---
title: 将用于 VM 的 Azure Monitor 的映射功能与 Operations Manager 集成 | Microsoft Docs
description: 用于 VM 的 Azure Monitor 可以自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 本文介绍如何使用映射功能在 Operations Manager 中自动创建分布式应用程序关系图。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: a20884c33d5232661e858e0b3ec722f139825aea
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82928000"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>将 System Center Operations Manager 与用于 VM 的 Azure Monitor 的映射功能集成

在用于 VM 的 Azure Monitor 中，可以查看在 Azure 或你的环境中运行的 Windows 和 Linux 虚拟机 (VM) 上发现的应用程序组件。 通过映射功能与 System Center Operations Manager 的此集成，可以根据用于 VM 的 Azure Monitor 中的动态依赖关系映射，在 Operations Manager 中自动创建分布式应用程序关系图。 本文介绍如何配置 System Center Operations Manager 管理组以支持此功能。

>[!NOTE]
>如果已部署服务映射，可以在用于 VM 的 Azure Monitor 中查看映射，其中包括监视 VM 运行状况和性能的附加功能。 用于 VM 的 Azure Monitor 的映射功能旨在替代独立的服务映射解决方案。 若要了解详细信息，请参阅[面向 VM 的 Azure Monitor 概述](vminsights-overview.md)。

## <a name="prerequisites"></a>必备条件

* System Center Operations Manager 管理组（2012 R2 或更高版本）。
* 经配置后支持用于 VM 的 Azure Monitor 的 Log Analytics 工作区。
* 一个或多个受 Operations Manager 监视并将数据发送到 Log Analytics 工作区的 Windows 和 Linux 虚拟机或物理计算机。 需将向 Operations Manager 管理组报告的 Linux 服务器配置为直接连接到 Azure Monitor。 有关详细信息，请参阅[使用 Log Analytics 代理收集日志数据](../platform/log-analytics-agent.md)中的概述。
* 对与 Log Analytics 工作区关联的 Azure 订阅具有访问权限的服务主体。 有关详细信息，请参阅[创建服务主体](#create-a-service-principal)。

## <a name="install-the-service-map-management-pack"></a>安装服务映射管理包

导入 Microsoft.SystemCenter.ServiceMap 管理捆绑包 (Microsoft.SystemCenter.ServiceMap.mpb) 可以启用 Operations Manager 与映射功能之间的集成。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=55763)下载管理捆绑包。 该捆绑包包含以下管理包：

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>配置集成

安装服务映射管理包后，将在 Operations Manager Operations 控制台的“管理”**** 窗格中的“Operations Management Suite”**** 下显示新节点“服务映射”****。

>[!NOTE]
>[Operations Management Suite](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)是包含 Log Analytics 的服务集合，现已成为[Azure Monitor](../overview.md)的一部分。

若要配置用于 VM 的 Azure Monitor 的映射集成，请执行以下操作：

1. 若要打开配置向导，请在“服务映射概述”窗格中单击“添加工作区”。********  

    ![“服务映射概述”窗格](media/service-map-scom/scom-configuration.png)

2. 在“连接配置”窗口中，输入服务主体的租户名称或 ID、应用程序 ID（也称为用户名或 clientID）和密码，并单击“下一步”。******** 有关详细信息，请参阅“创建服务主体”。

    ![“连接配置”窗口](media/service-map-scom/scom-config-spn.png)

3. 在“订阅选择”窗口中，选择 Azure 订阅、Azure 资源组（包含 Log Analytics 工作区的资源组）和 Log Analytics 工作区，然后单击“下一步”。********

    ![Operations Manager 配置工作区](media/service-map-scom/scom-config-workspace.png)

4. 在“计算机组选择”**** 窗口中，你可以选择要同步到 Operations Manager 的服务映射计算机组。 单击“添加/删除计算机组”****，从“可用计算机组”**** 列表中选择组，然后单击“添加”****。  选择组后，单击“确定”**** 完成。

    ![Operations Manager 配置计算机组](media/service-map-scom/scom-config-machine-groups.png)

5. 在“服务器选择”窗口中，配置包含要在 Operations Manager 与映射功能之间同步的服务器的服务映射服务器组。**** 单击“添加/删除服务器”。****

    若要在集成中为某个服务器构建分布式应用程序关系图，该服务器必须：

   * 受 Operations Manager 监视
   * 配置为向 Log Analytics 工作区（已配置了用于 VM 的 Azure Monitor）报告
   * 已列在服务映射服务器组中

     ![Operations Manager 配置组](media/service-map-scom/scom-config-group.png)

6. 可选：选择要与 Log Analytics 通信的所有管理服务器资源池，然后单击 "**添加工作区**"。

    ![Operations Manager 配置资源池](media/service-map-scom/scom-config-pool.png)

    配置和注册 Log Analytics 工作区可能需要一分钟时间。 完成配置后，Operations Manager 将启动首次映射同步。

    ![Operations Manager 配置资源池](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>监视集成

连接 Log Analytics 工作区后，Operations Manager 操作控制台的“监视”窗格中会显示新文件夹“Service Map”。****

![Operations Manager 的“监视”窗格](media/service-map-scom/scom-monitoring.png)

“服务映射”文件夹包含四个节点：

* **活动警报**：列出有关 Operations Manager 和 Azure Monitor 之间的通信的所有活动警报。  

  >[!NOTE]
  >这些警报不是与 Operations Manager 同步的 Log Analytics 警报，而是根据服务映射管理包中定义的工作流在管理组中生成的警报。

* "**服务器**"：列出已配置为从用于 VM 的 Azure Monitor 映射功能同步的受监视服务器。

    ![Operations Manager 的“监视服务器”窗格](media/service-map-scom/scom-monitoring-servers.png)

* **计算机组依赖关系视图**：列出从映射功能同步的所有计算机组。 可以单击任意组来查看其分布式应用程序关系图。

    ![Operations Manager 分布式应用程序关系图](media/service-map-scom/scom-group-dad.png)

* **服务器依赖关系视图**：列出从映射功能同步的所有服务器。 可以单击任一服务器来查看其分布式应用程序关系图。

    ![Operations Manager 分布式应用程序关系图](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>编辑或删除工作区

可以通过“服务映射概述”窗格编辑或删除配置的工作区（“管理”窗格 >“Operations Management Suite” > “服务映射”）。****************

> [!NOTE]
> [Operations Management Suite 是一组服务](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)，其中包括 Log Analytics，后者现在是 [Azure Monitor](../overview.md) 的一部分。

在当前这个版本中，只能配置一个 Log Analytics 工作区。

![Operations Manager 的“编辑工作区”窗格](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>配置规则和重写

一项规则 (*Microsoft.SystemCenter.ServiceMapImport.Rule*)，用于定期从用于 VM 的 Azure Monitor 的映射功能提取信息。 若要修改同步时间间隔，可以重写此规则并修改参数 **IntervalMinutes** 的值。

![Operations Manager 的“重写属性”窗口](media/service-map-scom/scom-overrides.png)

* **Enabled**：启用/禁用自动更新。
* **IntervalMinutes**：指定更新之间的时间。 默认间隔为 1 小时。 若要更频繁地同步映射，可以更改此值。
* **TimeoutSeconds**：指定请求超时前的时间长度。
* **TimeWindowMinutes**：指定查询数据的时间范围。 默认为 60 分钟，这是允许的最大时间间隔。

## <a name="known-issues-and-limitations"></a>已知问题和限制

当前设计存在以下问题和限制：

* 只能连接到单个 Log Analytics 工作区。
* 尽管可通过“创作”窗格手动将服务器添加到服务映射服务器组，但不会立即同步这些服务器的映射。**** 它们将在下一个同步周期内从用于 VM 的 Azure Monitor 的映射功能同步。
* 如果对通过管理包创建的分布式应用程序关系图进行任何更改，则可能会在下一次与用于 VM 的 Azure Monitor 同步时覆盖这些更改。

## <a name="create-a-service-principal"></a>创建服务主体

有关创建服务主体的 Azure 正式文档，请参阅：

* [使用 PowerShell 创建服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用 Azure CLI 创建服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [使用 Azure 门户创建服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>反馈
对于与用于 VM 的 Azure Monitor 地图功能或本文档的集成，你是否有任何反馈？ 请访问[用户之声页面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，可在此处推荐功能或对现有建议投票。
