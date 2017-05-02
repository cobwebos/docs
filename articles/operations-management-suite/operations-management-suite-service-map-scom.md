---
title: "服务映射与 System Center Operations Manager 的集成 | Microsoft 文档"
description: "服务映射是 Operations Management Suite (OMS) 解决方案，可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。  本文提供有关在 SCOM 中使用服务映射自动创建分布式应用程序关系图的详细信息。"
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
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 1937462eef4647b273dfa029c8f18c80d3443ae8
ms.lasthandoff: 04/14/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>服务映射与 System Center Operations Manager 的集成
  > [!NOTE]
  > 此功能处于个人预览状态，因此不应在生产系统上使用它。
  > 
  
Operations Management Suite (OMS) 服务映射可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你如所想一般作为提供重要服务的互连系统查看服务器。 服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。  有关详细信息，请参阅[服务映射文档](operations-management-suite-service-map.md)。

通过服务映射与 System Center Operations Manager (SCOM) 的此集成，可以根据服务映射中的动态依赖关系映射，在 SCOM 中自动创建分布式应用程序关系图。

## <a name="prerequisites"></a>先决条件
1.    用于管理一组服务器的 SCOM 管理组。
2.    已启用服务映射解决方案的 OMS 工作区。
3.    由 SCOM 管理并可向服务映射发送数据的一组服务器（至少一台）。  支持 Windows 和 Linux 服务器。
4.    与 OMS 工作区关联的、具有 Azure 订阅访问权限的服务主体。  [有关创建服务主体的详细信息](#creating-a-service-principal)。

## <a name="installing-service-map-management-pack"></a>安装服务映射管理包
导入 Microsoft.SystemCenter.ServiceMap 管理捆绑包 (Microsoft.SystemCenter.ServiceMap.mpb) 可以启用 SCOM 与服务映射之间的集成。  该捆绑包包含以下管理包：
* Microsoft ServiceMap Application Views
* Microsoft System Center ServiceMap Internal
* Microsoft System Center ServiceMap Overrides
* Microsoft System Center ServiceMap

## <a name="configuring-the-service-map-integration"></a>配置服务映射集成
1. 安装 ServiceMap 管理包后，“管理”窗格中 Operations Management Suite 的下面会出现新节点“服务映射”。
2. 在“服务映射概述”窗格中单击“添加工作区”打开配置向导。

    ![SCOM 配置向导](media/oms-service-map/scom-configuration.png)

3. 向导中的第一步是在其中输入 Azure 服务主体的信息的连接配置。 请输入服务主体的租户名称或 ID、应用程序 ID（也称为“用户名”或 ClientID）和密码。  [有关创建服务主体的详细信息](#creating-a-service-principal)。

    ![SCOM 配置 SPN](media/oms-service-map/scom-config-spn.png)

4. 下一个步骤是选择 Azure 订阅、Azure 资源组（包含 OMS 工作区的资源组）和 OMS 工作区。

    ![SCOM 配置工作区](media/oms-service-map/scom-config-workspace.png)

5. 下一个步骤是配置包含想要在 SCOM 与服务映射之间同步的服务器的服务映射服务器组。  单击“添加/删除服务器...” 按钮。 请注意，若要在集成中为某个服务器构建分布式应用程序关系图，该服务器必须：1) 由 SCOM 管理；2) 由服务映射管理；3) 已列在服务映射服务器组中。

    ![SCOM 配置组](media/oms-service-map/scom-config-group.png)

6. 可选 - 选择要与 OMS 通信的管理服务器资源池，然后单击“添加工作区”。

    ![SCOM 配置资源池](media/oms-service-map/scom-config-pool.png)

7. 请注意，配置和注册 OMS 工作区需要一分钟时间。 完成配置后，SCOM 将从 OMS 启动首次服务映射同步。

    ![SCOM 配置资源池](media/oms-service-map/scom-config-success.png)

**注意：**默认同步间隔设置为 60 分钟。 用户可配置重写来更改同步间隔。 用户还可以通过“创作”窗格手动将服务器添加到服务映射服务器组（“创作”窗格 -->“组”，然后搜索“服务映射服务器组”）。 下一次同步时，将同步这些服务器的服务器映射（根据配置的同步间隔）。

## <a name="monitoring-service-map"></a>监视服务映射
连接 OMS 工作区后，SCOM 控制台的“监视”窗格中将显示新文件夹“服务映射”。
![SCOM 监视](media/oms-service-map/scom-monitoring.png)

“服务映射”文件夹包含三个节点：
### <a name="active-alerts"></a>活动警报：
显示 SCOM 与 OMS 中服务映射解决方案之间的通信的所有相关活动警报。

**注意：**这些警报不是 SCOM 中显示的 OMS 警报。
### <a name="servers"></a>服务器：
包含配置为从服务映射同步的受监视服务器的列表。

![SCOM 监视服务器](media/oms-service-map/scom-monitoring-servers.png)

### <a name="server-dependency-views"></a>服务器依赖关系视图：
此视图包含从服务映射同步的所有服务器的列表。 用户可以单击任一服务器来查看其分布式应用程序关系图。

![SCOM 分布式应用程序关系图](media/oms-service-map/scom-dad.png)

## <a name="editdelete-workspace"></a>编辑/删除工作区：
用户可以通过“服务映射概述”窗格编辑或删除配置的工作区（“管理”窗格 -->“Operations Management Suite”-->“服务映射”）。  请注意，目前只能配置一个 OMS 工作区。

![SCOM 编辑工作区](media/oms-service-map/scom-edit-workspace.png)

## <a name="configuring-rules-and-overrides"></a>配置规则和重写：
已创建一个 **_Microsoft.SystemCenter.ServiceMapImport.Rule**_ 规则，用于定期从服务映射提取信息。  用户可以配置此规则的重写来更改同步计时。
“创作”窗格 -->“规则”-->“Microsoft.SystemCenter.ServiceMapImport.Rule”

![SCOM 重写](media/oms-service-map/scom-overrides.png)
* **Enabled** – 启用/禁用自动更新 
* **IntervalSeconds** – 更新的间隔时间。  默认值为 1 小时。 如果用户想要更频繁地同步服务器映射，可以更改此处的值。
* **TimeoutSeconds** – 请求在多长时间后超时 
* **TimeWindowMinutes** – 数据查询的持续时间。  默认值为 60 分钟时限。 最大值为 1 小时（服务映射允许的最大值）。

## <a name="known-issueslimitations"></a>已知问题/限制：
在当前设计中：
1. 尽管用户可以通过创作窗格手动将服务器添加到“服务映射服务器组”，但只会在下一个同步周期（默认值为 60 分钟。 用户可以重写同步计时），从服务映射同步这些服务器的映射。 
2. 用户可以连接到单个 OMS 工作区。

## <a name="creating-a-service-principal"></a>创建服务主体
单击以下链接可以访问有关创建服务主体的三种不同方法的 Azure 正式文档。
* [使用 PowerShell 创建服务主体](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用 Azure CLI 创建服务主体](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [使用 Azure 门户创建服务主体](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>反馈
是否有任何关于服务映射或本文档的反馈？  请访问 [User Voice 页面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，可在此处推荐功能或对现有建议投票。

