---
title: 在虚拟网络中部署 Azure Databricks
description: 本文介绍如何将 Azure Databricks 部署到虚拟网络，也称为 VNet 注入。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 3894904575d545aed0dbfce470247afb145b7590
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129301"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>在虚拟网络中部署 Azure Databricks

Azure Databricks 的默认部署是 Azure 上完全托管的服务：所有数据平面资源（包括虚拟网络（VNet））都部署到锁定的资源组。 但是，如果需要网络自定义，可以在自己的虚拟网络（也称为 VNet 注入）中部署 Azure Databricks 资源，以便能够：

* 使用服务终结点以更安全的方式将 Azure Databricks 连接到其他 Azure 服务（例如 Azure 存储）。
* 使用用户定义的路由连接到用于 Azure Databricks 的本地数据源。
* 将 Azure Databricks 连接到网络虚拟设备，以检查所有出站流量并根据允许和拒绝规则执行操作。
* 将 Azure Databricks 配置为使用自定义 DNS。
* 配置网络安全组（NSG）规则以指定出口流量限制。
* 在现有虚拟网络中部署 Azure Databricks 群集。

将 Azure Databricks 资源部署到自己的虚拟网络后，你还可以利用灵活的 CIDR 范围（在/16-/24 之间的任何位置，对于虚拟网络，介于/18-/26 之间）。

  > [!NOTE]
  > 不能将现有工作区替换为虚拟网络。 如果当前工作区无法容纳所需数量的活动群集节点，请在更大的虚拟网络中创建另一个工作区。 按照[以下详细的迁移步骤](howto-regional-disaster-recovery.md#detailed-migration-steps)，将资源（笔记本、群集配置、作业）从旧工作区复制到新工作区。

## <a name="virtual-network-requirements"></a>虚拟网络要求

你可以使用 Azure 门户中的 "Azure Databricks" 工作区部署接口，使用所需的子网、网络安全组和允许列表设置自动配置现有虚拟网络，也可以使用 Azure 资源管理器模板来配置虚拟网络和部署工作区。

部署 Azure Databricks 工作区所需的虚拟网络必须满足以下要求：

### <a name="location"></a>位置

虚拟网络必须位于与 Azure Databricks 工作区相同的位置。

### <a name="subnets"></a>子网

虚拟网络必须包含两个子网，专用于 Azure Databricks：

   1. 具有配置的网络安全组（允许群集内部通信）的专用子网

   2. 具有配置的网络安全组的公共子网，该安全组允许与 Azure Databricks 控制平面通信。

### <a name="address-space"></a>地址空间

虚拟网络的/16-/24 之间的 CIDR 块，专用和公用子网介于/18-/26 之间。

### <a name="whitelisting"></a>允许列表

子网与 Azure Databricks 控制平面之间的所有出站和入站流量必须列入允许列表。

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

本部分介绍如何在 Azure 门户中创建 Azure Databricks 工作区，并将其部署到自己的现有虚拟网络。 Azure Databricks 使用您提供的 CIDR 范围、允许列表入站和出站子网流量更新具有两个新子网和网络安全组的虚拟网络，并将该工作区部署到更新的虚拟网络。

## <a name="prerequisites"></a>先决条件

你必须有一个虚拟网络，你将 Azure Databricks 工作区部署到该网络。 你可以使用现有的虚拟网络，也可以创建一个新的虚拟网络，但该虚拟网络必须与你计划创建的 Azure Databricks 工作区位于同一区域。 虚拟网络需要介于/16-/24 之间的 CIDR 范围。

  > [!Warning]
  > 具有更小虚拟网络的工作区（即，更低的 CIDR 范围）比具有更大虚拟网络的工作区的 IP 地址（网络空间）更快。 例如，具有/24 个虚拟网络和/26 子网的工作区一次最多可以有64个活动的节点，而包含/20 个虚拟网络和/22 子网的工作区最多可容纳1024个节点。

  当你配置工作区时，将自动创建子网，并且你将有机会在配置期间为子网提供 CIDR 范围。

## <a name="configure-the-virtual-network"></a>配置虚拟网络

1. 在 Azure 门户中，选择 " **+ 创建资源" > Analytics > "Azure Databricks** " 以打开 Azure Databricks 服务对话框。

2. 按照在入门指南中的步骤2：创建 Azure Databricks 工作区中所述的配置步骤操作，然后选择 "在虚拟网络中部署 Azure Databricks 工作区" 选项。

   ![创建 Azure Databricks 服务](./media/vnet-injection/create-databricks-service.png)

3. 选择要使用的虚拟网络。

   ![虚拟网络选项](./media/vnet-injection/select-vnet.png)

4. 为两个子网（专用于 Azure Databricks）在/18-/26 的块中提供 CIDR 范围：

   * 将创建一个公共子网，其中包含关联的网络安全组，该安全组允许与 Azure Databricks 控制平面通信。
   * 将创建一个专用子网，其中包含允许群集内部通信的关联网络安全组。

5. 单击 "**创建**" 将 Azure Databricks 工作区部署到虚拟网络。

## <a name="advanced-resource-manager-configurations"></a>高级资源管理器配置

如果你想要对虚拟网络的配置进行更多的控制（例如，你想要使用现有的子网、使用现有的网络安全组或创建自己的安全规则），则可以使用以下 Azure 资源管理器模板，而不是门户虚拟网络配置和工作区部署。

### <a name="all-in-one"></a>全部在一个中

若要创建虚拟网络、网络安全组和 Azure Databricks 全部工作区，请使用[Databricks VNet 注入的工作区](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)的 "一体" 模板。

使用此模板时，无需执行任何手动允许列表的子网流量。

### <a name="network-security-groups"></a>网络安全组

若要创建具有现有虚拟网络所需的规则的网络安全组，请使用[Databricks VNet 注入的网络安全组模板](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)。

使用此模板时，无需执行任何手动允许列表的子网流量。

### <a name="virtual-network"></a>虚拟网络

若要使用适当的公共子网和专用子网创建虚拟网络，请使用[用于 Databricks VNet 注入的虚拟网络模板](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection)。

如果在不使用网络安全组模板的情况下使用此模板，则必须手动将允许列表规则添加到用于虚拟网络的网络安全组。

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作区

若要将 Azure Databricks 工作区部署到具有公共和专用子网的现有虚拟网络，并且已设置正确配置的网络安全组，请使用[Databricks VNet 注入的工作区模板](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection)。

如果在不使用网络安全组模板的情况下使用此模板，则必须手动将允许列表规则添加到用于虚拟网络的网络安全组。

## <a name="whitelisting-subnet-traffic"></a>允许列表子网流量

如果不使用[Azure 门户](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject#vnet-inject-portal)或[Azure 资源管理器模板](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject#vnet-inject-advanced)来创建网络安全组，则必须在子网上手动将以下流量列入允许列表。

|Direction|协议|Source|Source Port|目标|Destination Port|
|---------|--------|------|-----------|-----------|----------------|
|入站|\*|VirtualNetwork|\*|\*|\*|
|入站|\*|控制平面 NAT IP|\*|\*|22|
|入站|\*|控制平面 NAT IP|\*|\*|5557|
|出站|\*|\*|\*|Webapp IP|\*|
|出站|\*|\*|\*|SQL （服务标记）|\*|
|出站|\*|\*|\*|存储（服务标记）|\*|
|出站|\*|\*|\*|VirtualNetwork|\*|

使用以下 IP 地址的允许列表子网流量。 对于 SQL （元存储）和存储（项目和日志存储），应使用 Sql 和存储[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

|Azure Databricks 区域|服务|公共 IP|
|-----------------------|-------|---------|
|美国东部|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国东部 2|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国中北部|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国中部|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国中南部|控制平面 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|美国西部|控制平面 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|美国西部 2|控制平面 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|加拿大中部|控制平面 NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|加拿大东部|控制平面 NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|英国西部|控制平面 NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|英国南部|控制平面 NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|西欧|控制平面 NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|北欧|控制平面 NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|印度中部|控制平面 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|印度南部|控制平面 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|印度西部|控制平面 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|东南亚|控制平面 NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|东亚|控制平面 NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|澳大利亚东部|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亚东南部|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亚中部|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亚中部 2|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|日本东部|控制平面 NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|日本西部|控制平面 NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>故障排除

### <a name="workspace-launch-errors"></a>工作区启动错误

在自定义虚拟网络中启动工作区在 Azure Databricks 登录屏幕上失败，出现以下错误： **"我们在创建工作区时遇到错误。请确保自定义网络配置正确，然后重试。 "**

此错误是由于网络配置无法满足要求而导致的。 确认在创建工作区时遵循本主题中的说明。

### <a name="cluster-creation-errors"></a>群集创建错误

**无法访问的实例：无法通过 SSH 访问资源。**

可能的原因：阻止了从控制平面到工作人员的流量。 通过确保入站安全规则满足要求来进行修复。 如果要部署到连接到本地网络的现有虚拟网络，请使用将 Azure Databricks 工作区连接到本地网络中提供的信息查看设置。

**意外启动失败：设置群集时遇到意外错误。重试，如果问题仍然存在，请联系 Azure Databricks。内部错误消息：放置节点时超时。**

可能的原因：从工作线程到 Azure 存储终结点的流量被阻止。 通过确保出站安全规则满足要求来进行修复。 如果你使用的是自定义 DNS 服务器，还应检查虚拟网络中的 DNS 服务器的状态。

**云提供程序启动失败：设置群集时遇到云提供程序错误。有关详细信息，请参阅 Azure Databricks 指南。Azure 错误代码： AuthorizationFailed/InvalidResourceReference。**

可能的原因：虚拟网络或子网已不再存在。 请确保虚拟网络和子网存在。

**群集已终止。原因： Spark 启动失败： Spark 无法及时启动。此问题可能是由 Hive 元存储故障、Spark 配置无效或初始化脚本不正常引起的。若要解决此问题，请参阅 Spark 驱动程序日志，如果问题仍然存在，请联系 Databricks。内部错误消息： Spark 无法启动：驱动程序无法及时启动。**

可能的原因：容器无法与托管实例或 DBFS 存储帐户通信。 解决方法：将自定义路由添加到 DBFS 存储帐户的子网，并将下一跃点作为 Internet。

### <a name="notebook-command-errors"></a>笔记本命令错误

**命令未响应**

可能的原因：阻止了工作线程间的通信。 请确保入站安全规则满足要求，以进行修复。

**笔记本工作流失败，出现异常： databricks. WorkflowException： ConnectTimeoutException**

可能的原因：阻止了从工作人员到 Azure Databricks Webapp 的流量。 请确保出站安全规则满足要求，以进行修复。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure Databricks 提取、转换和加载数据](databricks-extract-load-sql-data-warehouse.md)
