---
title: "服务映射与 System Center Operations Manager 的集成 | Microsoft 文档"
description: "服务映射是 Operations Management Suite 解决方案，可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 本文介绍如何使用服务映射在 Operations Manager 中自动创建分布式应用程序关系图。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: af1f683f08ff6b70b23ff265f39b9a76f92f4be2
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>服务映射与 System Center Operations Manager 的集成
  > [!NOTE]
  > 此功能目前以公共预览版提供。
  > 
  
Operations Management Suite 服务映射可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 服务映射允许如所想一般作为提供重要服务的互连系统查看服务器。 服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。 有关详细信息，请参阅[服务映射文档](operations-management-suite-service-map.md)。

通过服务映射与 System Center Operations Manager 的此集成，可以根据服务映射中的动态依赖关系映射，在 Operations Manager 中自动创建分布式应用程序关系图。

## <a name="prerequisites"></a>先决条件
* 管理一组服务器的 Operations Manager 管理组（2012 R2 或更高版本）。
* 已启用服务映射解决方案的 Operations Management Suite 工作区。
* 由 Operations Manager 管理并可向服务映射发送数据的一组服务器（至少一台）。 支持 Windows 和 Linux 服务器。
* 与 Operations Management Suite 工作区关联的、具有 Azure 订阅访问权限的服务主体。 有关详细信息，请参阅[创建服务主体](#creating-a-service-principal)。

## <a name="install-the-service-map-management-pack"></a>安装服务映射管理包
导入 Microsoft.SystemCenter.ServiceMap 管理捆绑包 (Microsoft.SystemCenter.ServiceMap.mpb) 可以启用 Operations Manager 与服务映射之间的集成。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=55763)下载管理捆绑包。 该捆绑包包含以下管理包：
* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>配置服务映射集成
安装服务映射管理包后，“管理”窗格中“Operations Management Suite”的下面会显示新节点“服务映射”。 

若要配置服务映射集成，请执行以下操作：

1. 若要打开配置向导，请在“服务映射概述”窗格中单击“添加工作区”。  

    ![“服务映射概述”窗格](media/oms-service-map/scom-configuration.png)

2. 在“连接配置”窗口中，输入服务主体的租户名称或 ID、应用程序 ID（也称为用户名或 clientID）和密码，并单击“下一步”。 有关详细信息，请参阅[创建服务主体](#creating-a-service-principal)。

    ![“连接配置”窗口](media/oms-service-map/scom-config-spn.png)

3. 在“订阅选择”窗口中，选择 Azure 订阅、Azure 资源组（包含 Operations Management Suite 工作区的资源组）和 Operations Management Suite 工作区，并单击“下一步”。

    ![Operations Manager 配置工作区](media/oms-service-map/scom-config-workspace.png)

4. 在“计算机组选择”窗口中，你可以选择要同步到 Operations Manager 的服务映射计算机组。 单击“添加/删除计算机组”，从“可用计算机组”列表中选择组，然后单击“添加”。  选择组后，单击“确定”完成。
    
    ![Operations Manager 配置计算机组](media/oms-service-map/scom-config-machine-groups.png)
    
5. 在“服务器选择”窗口中，配置包含要在 Operations Manager 与服务映射之间同步的服务器的服务映射服务器组。 单击“添加/删除服务器”。   
    
    若要在集成中为某个服务器构建分布式应用程序关系图，该服务器必须：

    * 由 Operations Manager 管理
    * 由服务映射管理
    * 已列在服务映射服务器组中

    ![Operations Manager 配置组](media/oms-service-map/scom-config-group.png)

6. 可选：选择要与 Operations Management Suite 通信的管理服务器资源池，并单击“添加工作区”。

    ![Operations Manager 配置资源池](media/oms-service-map/scom-config-pool.png)

    配置和注册 Operations Management Suite 工作区可能需要一分钟时间。 完成配置后，Operations Manager 将从 Operations Management Suite 启动首次服务映射同步。

    ![Operations Manager 配置资源池](media/oms-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>监视服务映射
连接 Operations Management Suite 工作区后，Operations Manager 控制台的“监视”窗格中会显示新文件夹“服务映射”。

![Operations Manager 的“监视”窗格](media/oms-service-map/scom-monitoring.png)

“服务映射”文件夹包含四个节点：
* 活动警报：列出 Operations Manager 与服务映射之间通信相关的所有活动警报。  注意，这些警报不是要同步到 Operations Manager 的 Operations Management Suite 警报。 

* **服务器**：列出配置为从服务映射同步的受监视服务器。

    ![Operations Manager 的“监视服务器”窗格](media/oms-service-map/scom-monitoring-servers.png)

* 计算机组依赖关系视图：列出已从服务映射同步的所有计算机组。 可以单击任意组来查看其分布式应用程序关系图。

    ![Operations Manager 分布式应用程序关系图](media/oms-service-map/scom-group-dad.png)

* **服务器依赖关系视图**：列出已从服务映射同步的所有服务器。 可以单击任一服务器来查看其分布式应用程序关系图。

    ![Operations Manager 分布式应用程序关系图](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>编辑或删除工作区
可以通过“服务映射概述”窗格编辑或删除配置的工作区（“管理”窗格 >“Operations Management Suite” > “服务映射”）。 目前只能配置一个 Operations Management Suite 工作区。

![Operations Manager 的“编辑工作区”窗格](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>配置规则和重写
已创建一个 _Microsoft.SystemCenter.ServiceMapImport.Rule_ 规则，用于定期从服务映射提取信息。 若要更改同步计时，可以配置该规则的重写（“创作”窗格 >“规则” > “Microsoft.SystemCenter.ServiceMapImport.Rule”）

![Operations Manager 的“重写属性”窗口](media/oms-service-map/scom-overrides.png)

* **Enabled**：启用/禁用自动更新。 
* **IntervalMinutes**：重置更新间隔时间。 默认间隔为 1 小时。 如果想要更频繁地同步服务器映射，可以更改此值。
* **TimeoutSeconds**：重置请求超时之前的时间长短。 
* **TimeWindowMinutes**：重置数据查询的时限。 默认值为 60 分钟时限。 服务映射允许的最大值为 60 分钟。

## <a name="known-issues-and-limitations"></a>已知问题和限制

当前设计存在以下问题和限制：
* 仅可以连接到单个 Operations Management Suite 工作区。
* 尽管可通过“创作”窗格手动将服务器添加到服务映射服务器组，但不会立即同步这些服务器的映射。  它们将在下一个同步周期内从服务映射进行同步。
* 如果你对通过管理包创建的分布式应用程序关系图进行了任何更改，则可能会在下一次与服务映射同步时覆盖这些更改。

## <a name="create-a-service-principal"></a>创建服务主体
有关创建服务主体的 Azure 正式文档，请参阅：
* [使用 PowerShell 创建服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用 Azure CLI 创建服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [使用 Azure 门户创建服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>反馈
是否有任何关于服务映射或本文档的反馈？ 请访问[用户之声页面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，可在此处推荐功能或对现有建议投票。
