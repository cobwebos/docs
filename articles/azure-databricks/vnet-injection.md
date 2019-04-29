---
title: 在虚拟网络 （预览版） 中部署 Azure Databricks
description: 本文介绍如何将 Azure Databricks 部署到虚拟网络，也称为 VNet 注入。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126675"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>在虚拟网络 （预览版） 中部署 Azure Databricks

Azure Databricks 的默认部署是在 Azure 上完全托管的服务： 所有数据平面资源，包括虚拟网络 (VNet) 都部署到锁定的资源组。 如果需要网络自定义，但是，可以部署 Azure Databricks 资源在您自己的虚拟网络 （也称为 VNet 注入） 中, 时，可以：

* 以更安全的方式使用服务终结点，Azure Databricks 连接到其他 Azure 服务 （如 Azure 存储）。
* 使用连接到本地数据源以使用 Azure Databricks，充分利用用户定义的路由。
* 将 Azure Databricks 连接到网络虚拟设备，若要检查所有出站流量，并采取措施根据允许和拒绝规则。
* 配置 Azure Databricks 以使用自定义 DNS。
* 配置网络安全组 (NSG) 规则来指定传出流量限制。
* 将部署在现有虚拟网络中的 Azure Databricks 群集。

Azure Databricks 将资源部署到虚拟网络还允许您充分利用灵活的 CIDR 范围 (/16-之间的任意位置 / 24 的虚拟网络及之间 /18-/ 26 的子网)。

  > [!NOTE]
  > 不能替换现有的工作区的虚拟网络。 如果当前工作区不能容纳所需的数量的活动群集节点，请在较大的虚拟网络中创建另一个工作区。 请按照[这些详细的迁移步骤](howto-regional-disaster-recovery.md#detailed-migration-steps)将从旧的资源 （笔记本、 群集配置、 作业） 复制到新的工作区。

## <a name="virtual-network-requirements"></a>虚拟网络要求

可以使用 Azure 门户中的 Azure Databricks 工作区部署接口以使用所需子网、 网络安全组和允许列表设置的自动配置现有的虚拟网络，也可以使用 Azure 资源管理器若要配置虚拟网络和部署你的工作区的模板。

部署到 Azure Databricks 工作区的虚拟网络必须满足以下要求：

### <a name="location"></a>Location

虚拟网络必须位于与 Azure Databricks 工作区相同的位置。

### <a name="subnets"></a>子网

虚拟网络必须包含两个子网专用于 Azure Databricks:

   1. 具有允许群集内部通信配置的网络安全组的专用子网

   2. 具有允许与 Azure Databricks 控制平面之间的通信配置的网络安全组的公用子网。

### <a name="address-space"></a>地址空间

虚拟网络的/16/24 CIDR 块之间的专用和公共子网的/18/26 之间 CIDR 块。

### <a name="whitelisting"></a>允许列表

子网和 Azure Databricks 控制平面之间的所有出站和入站流量必须是已列入允许列表。

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

本部分介绍如何在 Azure 门户中创建 Azure Databricks 工作区并将其部署到现有的虚拟网络。 Azure Databricks 具有两个新子网和网络安全组使用 CIDR 范围由你提供更新的虚拟网络中，会加入允许列表的入站和出站子网流量，并将工作区部署到已更新的虚拟网络。

## <a name="prerequisites"></a>必备组件

您必须具有 Azure Databricks 工作区将部署到的虚拟网络。 可以使用现有的虚拟网络，也可以创建一个新的活动，但虚拟网络必须位于你打算创建 Azure Databricks 工作区所在的同一区域中。 需要为虚拟网络之间/16/24 的 CIDR 范围。

  > [!Warning]
  > 具有较小的虚拟网络的工作区 – 也就是说，较低的 CIDR 范围 – 可以缺少 IP 地址 （网络空间） 更快地运行比具有较大的虚拟网络的工作区。 例如，工作区使用/24 虚拟网络和/26 子网可以有最多为 64 个节点一次活动，而具有 /20 的工作区的虚拟网络和/22 子网可容纳最多为 1024年节点。

  配置工作区中，并将有机会在配置期间提供的子网的 CIDR 范围时，将自动创建子网。

## <a name="configure-the-virtual-network"></a>配置虚拟网络

1. 在 Azure 门户中，选择 **+ 创建资源 > Analytics > Azure Databricks**以打开 Azure Databricks 服务对话框。

2. 请按照步骤 2 中所述的配置步骤操作：在获取入门指南中，创建 Azure Databricks 工作区并在虚拟网络选项中选择部署 Azure Databricks 工作区。

   ![创建 Azure Databricks 服务](./media/vnet-injection/create-databricks-service.png)

3. 选择你想要使用的虚拟网络。

   ![虚拟网络选项](./media/vnet-injection/select-vnet.png)

4. 提供了两个子网，专用于 Azure Databricks 的/18/26 之间块中的 CIDR 范围：

   * 将使用关联的网络安全组允许与 Azure Databricks 控制平面通信创建公共子网。
   * 与允许在群集内部通信的关联的网络安全组，将创建专用子网。

5. 单击**创建**将部署到虚拟网络的 Azure Databricks 工作区。

## <a name="advanced-resource-manager-configurations"></a>高级的资源管理器配置

如果你想更好地控制虚拟网络的配置 – 例如，你想要使用现有子网，使用现有网络安全组，或创建您自己的安全规则 – 您可以使用以下 Azure 资源管理器模板而不是门户的虚拟网络配置和工作区部署。

### <a name="all-in-one"></a>多功能

若要创建在一个虚拟网络、 网络安全组和 Azure Databricks 工作区，请使用[Databricks VNet 注入工作区的多功能一体模板](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)。

当您使用此模板时，您不需要执行任何手动添加到的子网流量的白名单。

### <a name="network-security-groups"></a>网络安全组

若要使用现有的虚拟网络所需规则创建网络安全组，请使用[注入 Databricks VNet 的网络安全组模板](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection)。

当您使用此模板时，您不需要执行任何手动添加到的子网流量的白名单。

### <a name="virtual-network"></a>虚拟网络

若要使用适当的公共和专用子网中创建虚拟网络，请使用[注入 Databricks VNet 的虚拟网络模板](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection)。

如果你使用此模板而不使用网络安全组模板，你必须手动添加到虚拟网络中使用的网络安全组的允许列表规则。

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作区

若要将 Azure Databricks 工作区部署到现有的虚拟网络具有公共和专用子网和已设置的正确配置的网络安全组，请使用[Databricks VNet 注入的工作区模板](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection)。

如果你使用此模板而不使用网络安全组模板，你必须手动添加到虚拟网络中使用的网络安全组的允许列表规则。

## <a name="whitelisting-subnet-traffic"></a>列入白名单子网流量

如果不使用[Azure 门户](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal)或[Azure 资源管理器模板](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced)若要创建网络安全组，则必须手动以下流量的白名单上您的子网。

|Direction|协议|源|Source Port|目标|Destination Port|
|---------|--------|------|-----------|-----------|----------------|
|入站|\*|VirtualNetwork|\*|\*|\*|
|入站|\*|控制平面 NAT IP|\*|\*|22|
|入站|\*|控制平面 NAT IP|\*|\*|5557|
|出站|\*|\*|\*|Webapp IP|\*|
|出站|\*|\*|\*|SQL （服务标记）|\*|
|出站|\*|\*|\*|存储 （服务标记）|\*|
|出站|\*|\*|\*|VirtualNetwork|\*|

解决了使用以下 IP 白名单子网流量。 对于 SQL （元存储） 和存储 （项目和日志存储），应使用 Sql 和存储[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

|Azure Databricks 区域|服务|公共 IP|
|-----------------------|-------|---------|
|美国东部|控制平面 NAT </br></br>Web 应用|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国东部 2|控制平面 NAT </br></br>Web 应用|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国中北部|控制平面 NAT </br></br>Web 应用|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国中部|控制平面 NAT </br></br>Web 应用|23.101.152.95/32 </br></br>40.70.58.221/32|
|美国中南部|控制平面 NAT </br></br>Web 应用|40.83.178.242/32 </br></br>40.118.174.12/32|
|美国西部|控制平面 NAT </br></br>Web 应用|40.83.178.242/32 </br></br>40.118.174.12/32|
|美国西部 2|控制平面 NAT </br></br>Web 应用|40.83.178.242/32 </br></br>40.118.174.12/32|
|加拿大中部|控制平面 NAT </br></br>Web 应用|40.85.223.25/32 </br></br>13.71.184.74/32|
|加拿大东部|控制平面 NAT </br></br>Web 应用|40.85.223.25/32 </br></br>13.71.184.74/32|
|英国西部|控制平面 NAT </br></br>Web 应用|51.140.203.27/32 </br></br>51.140.204.4/32|
|英国南部|控制平面 NAT </br></br>Web 应用|51.140.203.27/32 </br></br>51.140.204.4/32|
|西欧|控制平面 NAT </br></br>Web 应用|23.100.0.135/32 </br></br>52.232.19.246/32|
|北欧|控制平面 NAT </br></br>Web 应用|23.100.0.135/32 </br></br>52.232.19.246/32|
|印度中部|控制平面 NAT </br></br>Web 应用|104.211.89.81/32 </br></br>104.211.101.14/32|
|印度南部|控制平面 NAT </br></br>Web 应用|104.211.89.81/32 </br></br>104.211.101.14/32|
|印度西部|控制平面 NAT </br></br>Web 应用|104.211.89.81/32 </br></br>104.211.101.14/32|
|东南亚|控制平面 NAT </br></br>Web 应用|52.187.0.85/32 </br></br>52.187.145.107/32|
|东亚|控制平面 NAT </br></br>Web 应用|52.187.0.85/32 </br></br>52.187.145.107/32|
|澳大利亚东部|控制平面 NAT </br></br>Web 应用|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亚东南部|控制平面 NAT </br></br>Web 应用|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亚中部|控制平面 NAT </br></br>Web 应用|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亚中部 2|控制平面 NAT </br></br>Web 应用|13.70.105.50/32 </br></br>13.75.218.172/32|
|日本东部|控制平面 NAT </br></br>Web 应用|13.78.19.235/32 </br></br>52.246.160.72/32|
|日本西部|控制平面 NAT </br></br>Web 应用|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>故障排除

### <a name="workspace-launch-errors"></a>工作区启动错误

启动自定义虚拟网络中的工作区失败，Azure Databricks 上的登录屏幕并出现以下错误：**"我们已经遇到了错误创建工作区。请确保自定义网络配置正确，然后重试。"**

不符合要求的网络配置被导致此错误。 确认创建工作区时遵循本主题中的说明。

### <a name="cluster-creation-errors"></a>群集创建错误

**实例无法访问：资源未通过 SSH 访问。**

可能的原因： 阻止了流控制平面流量到辅助角色。 解决方法是确保入站的安全规则满足要求。 如果您要部署到现有的虚拟网络连接到你的本地网络，查看你的安装程序使用 Azure Databricks 工作区连接到本地网络中提供的信息。

**意外的启动故障：设置群集时遇到意外的错误。重试，如果问题仍然存在，请联系 Azure Databricks。内部错误消息：放置节点时超时。**

可能的原因： 辅助角色从 Azure 存储终结点的流量被阻止。 解决方法是确保出站安全规则满足要求。 如果使用自定义 DNS 服务器，还检查你的虚拟网络中的 DNS 服务器的状态。

**云提供程序启动失败：设置群集时遇到云提供程序错误。请参阅 Azure Databricks 指南以获取详细信息。Azure 错误代码：AuthorizationFailed/InvalidResourceReference.**

可能的原因： 虚拟网络或子网不再不存在。 请确保存在虚拟网络和子网。

**终止群集。原因：Spark 启动失败：Spark 程序无法及时启动。此问题可能引起运行不正常的 Hive 元存储、 无效的 Spark 配置或运行不正常的 init 脚本。如果问题仍然存在，请联系 Databricks 及，请参阅 Spark 驱动程序日志以解决此问题。内部错误消息：Spark 启动失败：驱动程序无法及时启动。**

可能的原因：容器不能与托管实例或 DBFS 存储帐户。 通过向 DBFS 存储帐户的子网添加自定义路由，与正在 Internet 的下一跃点修复。

### <a name="notebook-command-errors"></a>Notebook 命令错误

**命令未响应**

可能的原因： 辅助角色到辅助角色通信将被阻止。 修复通过确保入站的安全规则满足要求。

**笔记本工作流失败，出现异常： com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

可能的原因： 从辅助角色到 Azure Databricks Webapp 流量被阻止。 修复通过确保出站安全规则满足要求。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure Databricks 提取、转换和加载数据](databricks-extract-load-sql-data-warehouse.md)
