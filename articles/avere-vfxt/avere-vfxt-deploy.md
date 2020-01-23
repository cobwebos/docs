---
title: 部署 Avere vFXT for Azure
description: 在 Azure 中部署 Avere vFXT 群集的步骤
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547517"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 群集

此过程详述如何使用 Azure 市场提供的部署向导。 此向导自动使用 Azure 资源管理器模板来部署群集。 在窗体中输入参数并单击 "**创建**" 后，Azure 将自动完成以下任务：

* 创建群集控制器，该控制器是一个基本 VM，其中包含部署和管理群集所需的软件。
* 设置资源组和虚拟网络基础结构，包括创建新元素。
* 创建群集节点 Vm，并将其配置为 Avere 群集。
* 如果请求，将创建一个新的 Azure Blob 容器，并将其配置为群集核心文件服务器。

按照本文档中的说明操作后，你将拥有一个虚拟网络、一个子网、一个群集控制器和一个 vFXT 群集，如下图所示。 此图显示了可选的 Azure Blob 核心文件服务器，其中包括新的 Blob 存储容器（在新的存储帐户中，未显示）和子网中 Microsoft 存储的服务终结点。

![显示具有 Avere 群集组件的三个同心圆矩形的关系图。 外部矩形标记为 "资源组"，包含标记为 "Blob 存储（可选）" 的六边形。 中的下一个矩形标记为 "虚拟网络： 10.0.0.0/16"，不包含任何唯一组件。 最内层的矩形标记为 "Subnet： 10.0.0.0/24"，其中包含标记为 "群集控制器" 的 VM、标记为 "vFXT 节点（vFXT Cluster）" 的三个 Vm 的堆栈以及标记为 "Service endpoint" 的六边形。 有一个箭头连接服务终结点（位于子网内）以及 blob 存储（位于子网和 vnet 之外的资源组中）。 箭头通过子网和虚拟网络边界。](media/avere-vfxt-deployment.png)

在使用创建模板之前，请确保已具备以下必备项：  

* [新订阅](avere-vfxt-prereqs.md#create-a-new-subscription)
* [订阅所有者权限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [存储服务终结点（如果需要）](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) -部署使用现有虚拟网络和创建 blob 存储所需的项

有关群集部署的步骤和计划的详细信息，请阅读[计划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)和[部署概述](avere-vfxt-deploy-overview.md)。

## <a name="create-the-avere-vfxt-for-azure"></a>创建 Avere vFXT for Azure

通过搜索 Avere 并选择 "Avere vFXT for Azure ARM 模板"，访问 Azure 门户中的创建模板。

![显示 Azure 门户的浏览器窗口（带有面包屑导航“新建>市场>所有内容”）。 在 "所有内容" 页中，搜索字段的字词为 "avere"，第二个结果为 "Avere vFXT for Azure ARM Template"，以红色突出显示。](media/avere-vfxt-template-choose.png)

阅读 Avere vFXT for Azure ARM 模板页上的详细信息后，单击其 "**创建**" 按钮开始。

![Azure 市场，显示部署模板的第一页](media/avere-vfxt-deploy-first.png)

模板包含四个步骤/页面 - 两个信息收集页面，一个验证步骤和一个确认步骤。

* 第一页收集群集控制器 VM 的设置。
* 第二页收集用于创建群集的参数和其他资源（如子网和存储）。
* 第三页概述了你的选择并验证了配置。
* 第四页说明软件条款和条件，让你能够启动群集创建过程。

## <a name="page-one-parameters---cluster-controller-information"></a>第一页的参数 - 群集控制器信息

部署模板的第一页重点介绍群集控制器。

![部署模板的第一页](media/avere-vfxt-deploy-1.png)

填充以下信息：

* **群集控制器名称** - 设置群集控制器 VM 的名称。

* **控制器用户名**-设置群集控制器 VM 的根用户名。

* **身份验证类型** - 选择连接到控制器所需的密码或 SSH 公钥身份验证。 建议使用 SSH 公钥方法；如需帮助，请阅读[如何创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

* **密码**或 **SSH 公钥** - 必须在后续字段中提供 RSA 公钥或密码，具体取决于所选身份验证类型。 此凭据与以前提供的用户名配合使用。

* **订阅** - 选择 Avere vFXT 的订阅。

* **资源组** - 选择 Avere vFXT 群集的资源组，或者单击“新建”，然后输入新的资源组名称。

* **位置** - 选择群集和资源的 Azure 位置。

完成后，单击“确定”。

> [!NOTE]
> 如果希望群集控制器有一个面向公众的 IP 地址，请为群集创建一个新的虚拟网络，而不是选择一个现有的网络。 此设置在第二页上。

## <a name="page-two-parameters---vfxt-cluster-information"></a>第二页的参数 - vFXT 群集信息

可以使用部署模板的第二页来设置群集大小、节点类型、缓存大小、存储参数等设置。

![部署模板的第二页](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 群集节点计数**-选择群集中的节点数。 至少 3 个节点，至多 12 个节点。

* **群集管理密码** - 创建用于群集管理的密码。 此密码与用户名一起使用 ```admin``` 登录到群集控制面板，你可以在其中监视群集并配置群集设置。

* **Avere vFXT 群集名称** - 为群集提供唯一名称。

* **大小**-此部分显示将用于群集节点的 VM 类型。 尽管只有一个推荐的选项，但 "**更改大小**" 链接会打开一个表，其中包含此实例类型的详细信息和价格计算器的链接。

* **每个节点的缓存大小**-群集缓存分布在群集节点上，因此 Avere vFXT 群集上的总缓存大小将是此大小乘以节点数。

  建议的配置：对 Standard_E32s_v3 节点使用每个节点 4 TB。

* **虚拟网络**-定义新的虚拟网络以容纳群集，或选择满足[Plan Avere vFXT 系统](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)中所述先决条件的现有网络。

  > [!NOTE]
  > 如果创建新的虚拟网络，则群集控制器将具有公共 IP 地址，以便可以访问新的专用网络。 如果选择现有的虚拟网络，则不需要使用公共 IP 地址即可配置群集控制器。
  >
  > 如果群集控制器上有公开显示的 IP 地址，则虽然方便用户对 vFXT 群集的访问，但会有一些安全风险。
  >* 用户可以将群集控制器上的公共 IP 地址用作跳转主机，从专用子网外部连接到 Avere vFXT 群集。
  >* 如果控制器上没有公共 IP 地址，则需要另一个跳转主机、VPN 连接或 ExpressRoute 来访问群集。 例如，使用已配置 VPN 连接的现有虚拟网络。
  >* 如果创建具有公共 IP 地址的控制器，则应使用网络安全组保护控制器 VM。 默认情况下，Avere vFXT for Azure deployment 会创建一个网络安全组，该安全组限制对具有公共 IP 地址的控制器的端口22的入站访问。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

  还使用 Azure Blob 存储的存储服务终结点配置新的虚拟网络，并且网络访问控制锁定为仅允许群集子网中的 Ip。

* **子网**-选择一个子网或创建一个新的子网。

* **创建和使用 blob 存储**-选择 " **true** " 可创建新的 Azure blob 容器，并将其配置为新的 Avere vFXT 群集的后端存储。 此选项还会在群集的资源组中创建一个新的存储帐户，并在群集子网中创建一个 Microsoft 存储服务终结点。
  
  如果提供现有虚拟网络，则在创建群集之前，它必须具有存储服务终结点。 （有关详细信息，请参阅[计划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)。）

  如果不希望创建新容器，请将此字段设置为 **false**。 在这种情况下，你必须在创建群集后附加和配置存储。 有关说明，请参阅[配置存储](avere-vfxt-add-storage.md)。

* **（新）存储帐户**-如果要创建新的 Azure Blob 容器，请输入新存储帐户的名称。

## <a name="validation-and-purchase"></a>验证和购买

第三页概述配置并验证参数。 验证成功后，请检查摘要并单击 **"确定"** 按钮。

> [!TIP]
> 可以通过单击 **"确定"** 按钮旁的 "**下载模板和参数**" 链接来保存此群集的创建设置。 如果以后需要创建类似的群集，例如在灾难恢复方案中创建替代群集，则此信息会很有帮助。 （有关详细信息，请参阅[灾难恢复指南](disaster-recovery.md)。）

![部署模板的第三页 - 验证](media/avere-vfxt-deploy-3.png)

第四页提供了使用条款以及隐私和定价信息的链接。

输入缺少的联系人信息，然后单击 "**创建**" 按钮接受条款并为 Azure 群集创建 Avere vFXT。

![部署模板的第四页 - 条款和条件、“创建”按钮](media/avere-vfxt-deploy-4.png)

群集部署需要 15-20 分钟。

## <a name="gather-template-output"></a>收集模板输出

Avere vFXT 模板完成创建群集后，会输出有关新群集的重要信息。

> [!TIP]
> 请确保从模板输出中复制**管理 IP 地址**。 需要该地址来管理群集。

查找信息：

1. 转到群集控制器的资源组。

1. 在左侧单击“部署”，然后单击“microsoft-avere.vfxt-template”。

   ![资源组门户页面，其左侧选中“部署”，“部署名称”下的表中显示 microsoft-avere.vfxt-template](media/avere-vfxt-outputs-deployments.png)

1. 在左侧，单击“输出”。 复制每个字段中的值。

   ![输出页面，在标签右侧的字段中显示 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK_RESOURCE_GROUP、NETWORK、SUBNET、SUBNET_ID、VSERVER_IPs 和 MGMT_IP 的值](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>后续步骤

既然群集正在运行，并且知道其管理 IP 地址，请[连接到群集配置工具](avere-vfxt-cluster-gui.md)。

使用配置界面来自定义群集，其中包括以下设置任务：

* [启用支持](avere-vfxt-enable-support.md)
* [添加存储](avere-vfxt-add-storage.md)（如果需要）
