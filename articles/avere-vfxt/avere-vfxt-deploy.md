---
title: 部署 Avere vFXT for Azure
description: 在 Azure 中部署 Avere vFXT 群集的步骤
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d7c207f89b9cb50f940f071fbbf6ee81b4d44976
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164314"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 群集

在 Azure 中创建 vFXT 群集的最简单方法是使用群集控制器。 群集控制器是包含用于创建和管理 vFXT 群集的必需脚本、模板和软件基础结构的 VM。

部署新的 vFXT 群集的步骤包括：

1. [创建群集控制器](#create-the-cluster-controller-vm)。
1. 如果使用 Azure Blob 存储，在虚拟网络中[创建存储终结点](#create-a-storage-endpoint-if-using-azure-blob)。
1. [连接到群集控制器](#access-the-controller)。 其余步骤由群集控制器 VM 完成。 
1. 为群集节点[创建访问角色](#create-the-cluster-node-access-role)。 提供了原型。
1. 为要创建的 vFXT 群集类型[自定义群集创建脚本](#edit-the-deployment-script)。
1. [执行群集创建脚本](#run-the-script)。

有关群集部署的步骤和计划的详细信息，请阅读[计划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)和[部署概述](avere-vfxt-deploy-overview.md)。 

按照本文档中的说明操作后，将获得虚拟网络、子网、控制器和 vFXT 群集，如下图所示：

![关系图，它所示的 VNET 包含可选 blob 存储以及一个子网，该子网包含三个标记为 vFXT 节点/vFXT 群集的分组 VM 和一个标记为群集控制器的 VM](media/avere-vfxt-deployment.png)

在开始部署之前，确保已具备以下必备项：  

1. [新订阅](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [订阅所有者权限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

（可选）可以在创建群集控制器[之前](avere-vfxt-pre-role.md)创建群集节点角色，但在此后执行该操作更简单。

## <a name="create-the-cluster-controller-vm"></a>创建群集控制器 VM

第一步是创建用于创建和配置 vFXT 群集节点的虚拟机。 

群集控制器是预安装了 Avere vFXT 群集创建软件和脚本的 Linux VM。 它不需要强大的处理能力或大量存储空间，因此可以选择成本较低的选项。 在 vFXT 群集的整个生存期中，偶尔会使用此 VM。

有两种方法可以创建群集控制器 VM。 [下面](#create-controller---arm-template)提供了一个 [Azure 资源管理器模板](#create-controller---arm-template)用于简化创建过程；你也可以从 [Azure 市场映像](#create-controller---azure-marketplace-image)创建控制器。 

可以在创建控制器时创建新资源组。

> [!TIP]
>
> 决定是否在群集控制器上使用公共 IP 地址。 通过公共 IP 地址，可以更轻松地访问 vFXT 群集，但存在较小的安全风险。
>
>  * 用户可以将群集控制器上的公共 IP 地址用作跳转主机，从专用子网外部连接到 Avere vFXT 群集。
>  * 如果未在控制器上设置公共 IP 地址，则必须使用其他跳转主机、VPN 连接或 ExpressRoute 来访问群集。 例如，在配置了 VPN 连接的虚拟网络中创建控制器。
>  * 如果创建具有公共 IP 地址的控制器，则应使用网络安全组保护控制器 VM。 仅允许通过端口 22 访问以提供 Internet 访问。

### <a name="create-controller---resource-manager-template"></a>创建控制器 - 资源管理器模板

若要从门户创建群集控制器节点，请单击下面的“部署到 Azure”按钮。 此部署模板创建用于创建和管理 Avere vFXT 群集的 VM。

[![用于创建控制器的按钮](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

提供以下信息。

在“基本”部分中提供：  

* 群集的**订阅**
* **群集的资源组** 
* **位置** 

在“设置”部分中提供：

* 是否要创建新的虚拟网络

  * 如果创建新的 VNET，系统会为群集控制器分配一个公共 IP 地址，以便访问。 还为此 VNET 创建了一个网络安全组，它将入站流量限制为仅限端口 22。
  * 如果要使用 ExpressRoute 或 VPN 连接到群集控制器，请将此值设置为 `false` 并在其余字段中指定一个现有的 VNET。 群集控制器将该 VNET 用于网络通信。 

* 虚拟网络资源组、名称和子网名称 - 键入现有资源的名称（如果使用现有 VNET）或在创建新 VNET 时键入新名称
* 控制器名称 -设置控制器 VM 的名称
* 控制器管理员用户名 - 默认为 `azureuser`
* SSH 密钥 - 粘贴要与管理员用户名关联的公钥。 如需帮助，请阅读[如何创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

在“条款和条件”下： 

* 阅读服务条款并单击复选框接受它们。 

  > [!NOTE] 
  > 如果你不是订阅所有者，请让所有者按照[提前接受软件条款](avere-vfxt-prereqs.md#accept-software-terms-in-advance)中的前提步骤接受条款。 


完成后单击“购买”。 五到六分钟后，控制器节点将启动并运行。

访问输出页来收集创建群集所需的控制器信息。 请阅读[创建群集所需的信息](#information-needed-to-create-the-cluster)来了解更多信息。

### <a name="create-controller---azure-marketplace-image"></a>创建控制器 - Azure 市场映像

在 Azure 市场中搜索名称 ``Avere``查找控制器模板。 选择“Avere vFXT for Azure 控制器”模板。

如果尚未这样做，请接受条款，然后单击“要以编程方式部署?”来启用对市场映像的编程访问权限。 “创建”按钮下方的链接。

![屏幕截图，显示位于“创建”按钮下方的以编程方式访问的链接](media/avere-vfxt-deploy-programmatically.png)

单击“启用”按钮，然后保存设置。

![屏幕截图，显示通过鼠标单击启用编程访问](media/avere-vfxt-enable-program.png)

返回到“Avere vFXT for Azure 控制器”模板的主页，然后单击“创建”。 

在第一个面板中，填写或确认以下基本选项：

* **订阅**
* **资源组**（如果要创建新组，请输入新名称。）
* **虚拟机名称** - 控制器的名称
* **区域**
* **可用性选项** - 不需要冗余
* **映像** - Avere vFXT 控制器节点映像
* **大小** - 保留默认值或选择其他成本较低的选项
* **管理员帐户** - 设置登录到群集控制器的方式： 
  * 选择用户名/密码或 SSH 公钥（推荐）。
  
    > [!TIP] 
    > SSH 密钥更安全。 如需帮助，请阅读[如何创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。 
  * 指定用户名 
  * 粘贴 SSH 密钥，或输入并确认密码
* **入站端口规则** - 如果使用公共 IP 地址，请打开端口 22 (SSH)

单击“下一步”设置磁盘选项：

* **OS 磁盘类型** - 使用 HDD 的默认值即可
* **使用非托管磁盘** - 非必需
* **数据磁盘** - 不使用

单击“下一步”设置联网选项：

* **虚拟网络** - 选择控制器的 VNET，或输入名称以创建新的 VNET。 如果不想对控制器使用公共 IP 地址，请考虑将它放在已设置 ExpressRoute 或其他访问方法的 VNET 中。
* **子网** - 在 VNET 中选择一个子网（可选）。 如果创建新 VNET，可以同时创建新子网。
* **公共 IP** - 如果要使用公共 IP 地址，可以在此处指定。 
* **网络安全组** - 保留默认设置（基本） 
* **公共入站端口** - 如果使用公共 IP 地址，请使用此控件允许通过 SSH 流量进行访问。 
* **加速网络** - 此 VM 无法使用。

单击“下一步”设置管理选项：

* **启动诊断** - 更改为“关”
* **OS 访客诊断** - 禁用
* **诊断存储帐户** -（可选）选择或指定新帐户，用于保留诊断信息。
* **托管服务标识** - 将此选项更改为“开”，会为群集控制器创建一个 Azure AD 服务原则。
* **自动关闭** - 关闭 

此时，如果不想使用实例标记，可以单击“查看 + 创建”。 否则，请点击“下一步”两次，以跳过“访客配置”页面并转到标记页面 完成后，点击“查看 + 创建”。 

验证选择后，单击“创建”按钮。  

创建大约需要五六分钟时间。

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>创建存储终结点（如果使用的是 Azure Blob）

如果要将 Azure Blob 存储用于后端数据存储，应在虚拟网络中创建一个存储服务终结点。 此[服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)使 Azure Blob 流量保留在本地，而不是通过 Internet 路由。

1. 在门户中，单击左侧的“虚拟网络”。
1. 为控制器选择 VNET。 
1. 单击左侧的“服务终结点”。
1. 单击顶部的“添加”。
1. 将服务保留为 ``Microsoft.Storage`` 并选择控制器的子网。
1. 在底部单击“添加”。

  ![带注释的 Azure 门户屏幕截图，注释标明创建服务终结点的步骤](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>创建群集所需的信息

在创建群集控制器后，请确保具有后续步骤所需的信息。 

连接到控制器所需的信息： 

* 控制器用户名和 SSH 密钥（或密码）
* 控制器 IP 地址或连接到控制器 VM 的其他方法

群集所需的信息： 

* 资源组名称
* Azure 位置 
* 虚拟网络名称
* 子网名称
* 群集节点角色名称 - 此名称是在创建角色时设置的，如[下文](#create-the-cluster-node-access-role)所述
* 存储帐户名（如果创建 Blob 容器）

如果使用资源管理器模板创建了控制器节点，可以从[模板输出](#find-template-output)获取信息。 

如果你使用了 Azure 市场映像来创建控制器，则你已直接提供了这些项中的大部分。 

浏览到控制器 VM 信息页来查找任何缺少的项。 例如，单击“所有资源”并搜索控制器名称，然后单击控制器名称以查看其详细信息。

### <a name="find-template-output"></a>查找模板输出

要从资源管理器模板输出中查找此信息，请按照以下过程操作：

1. 转到群集控制器的资源组。

1. 在左侧单击“部署”，然后单击“Microsoft.Template”。

   ![资源组门户页面，其左侧选中“部署”，“部署名称”下的表中显示 Microsoft.Template](media/avere-vfxt-deployment-template.png)

1. 在左侧，单击“输出”。 复制每个字段中的值。 

   ![输出页面，其中 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK、SUBNET 和 SUBNET_ID 显示在标签右侧的字段中](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>访问控制器

若要完成剩余的部署步骤，需要连接到群集控制器。

1. 连接到群集控制器的方法取决于设置。

   * 如果控制器具有公共 IP 地址，将控制器 IP 的 SSH 设置为管理员用户名（例如，``ssh azureuser@40.117.136.91``）。
   * 如果控制器没有公共 IP，请使用 VPN 或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 连接到 VNET。

1. 登录到控制器后，运行 `az login` 验证身份。 复制 shell 中提供的身份验证代码，然后使用 Web 浏览器加载 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) 并对 Microsoft 系统进行身份验证。 返回 shell 进行确认。

   ![“AZ login”命令的命令行输出，显示浏览器链接和身份验证代码](media/avere-vfxt-azlogin.png)

1. 通过使用订阅 ID 运行此命令添加订阅 ```az account set --subscription YOUR_SUBSCRIPTION_ID```：

## <a name="create-the-cluster-node-access-role"></a>创建群集节点访问角色

> [!NOTE] 
> * 如果你不是订阅所有者，并且尚未创建角色，请让订阅所有者按照以下步骤操作，或按[在没有控制器的情况下创建 Avere vFXT 群集运行时访问角色](avere-vfxt-pre-role.md)中的程序操作。
> 
> * Microsoft 内部用户应当使用名为“Avere 群集运行时操作员”的现有角色而非尝试创建一个。 

[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) 授权 vFXT 群集节点执行必要的任务。  

作为 vFXT 群集常规操作的一部分，各个 vFXT 节点需要执行读取 Azure 资源属性、管理存储和控制其他节点的网络接口设置等操作。 

1. 在控制器上，打开编辑器中的 ``/avere-cluster.json`` 文件。

   ![显示列表命令以及“vi /avere-cluster.json”的控制台](media/avere-vfxt-open-role.png)

1. 编辑文件以包含订阅 ID，并删除其上方的行。 将文件另存为 ``avere-cluster.json``。

   ![控制台文本编辑器，显示订阅 ID 和要删除的“删除此行”](media/avere-vfxt-edit-role.png)

1. 使用此命令以创建角色：  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

你将在下一步中将角色的名称传递给群集创建脚本。 

## <a name="create-nodes-and-configure-the-cluster"></a>创建节点并配置群集

要创建 Avere vFXT 群集，请编辑控制器中包含的示例脚本之一并在其中运行。 示例脚本位于群集控制器上的根目录 (`/`) 中。

* 如果要创建 Blob 容器以用作 Avere vFXT 的后端存储系统，请使用 ``create-cloudbacked-cluster`` 脚本。

* 如果稍后要添加存储，请使用 ``create-minimal-cluster`` 脚本。

> [!TIP]
> 群集控制器 VM 的 `/` 目录中也包括用于添加节点和销毁 vFXT 群集的原型脚本。

### <a name="edit-the-deployment-script"></a>编辑部署脚本

在编辑器中打开示例脚本。 你可能希望使用其他名称保存自定义脚本，以免覆盖原始样本。

输入以下脚本变量的值。

* 资源组名称

  * 如果使用位于不同资源组中的网络或存储组件，请取消注释变量并提供名称。 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* 位置名称
* 虚拟网络名称
* 子网名称
* Azure AD 运行时角色名 - 如果按[创建群集节点访问角色](#create-the-cluster-node-access-role)中的示例操作，请使用 ``avere-cluster``。 
* 存储帐户名（如果创建新的 Blob 容器）
* 群集名称 - 同一个资源组中不能有两个具有相同名称的 vFXT 群集。 最佳做法是为每个群集指定唯一名称。
* 管理密码 - 选择用于监视和管理群集的安全密码。 此密码已分配给用户 ``admin``。 
* 节点实例类型 - 相关信息，请参阅 [vFXT 节点大小](avere-vfxt-deploy-plan.md#vfxt-node-sizes)
* 节点缓存大小 - 相关信息，请参阅 [vFXT 节点大小](avere-vfxt-deploy-plan.md#vfxt-node-sizes)

保存文件并退出。

### <a name="run-the-script"></a>运行脚本

通过键入创建的文件名运行脚本。 （示例：`./create-cloudbacked-cluster-west1`）  

> [!TIP]
> 请考虑在[终端多路复用器](http://linuxcommand.org/lc3_adv_termmux.php)（如 `screen` 或 `tmux`）内运行此命令，以防丢失连接。  

输出也记录到 `~/vfxt.log`。

脚本完成后，复制群集管理所需的管理 IP 地址。

![脚本的命令行输出，在接近结尾的位置显示管理 IP 地址](media/avere-vfxt-mgmt-ip.png)

## <a name="next-step"></a>后续步骤

现在群集正在运行，并且你已知道其管理 IP 地址，可以[连接到群集配置工具](avere-vfxt-cluster-gui.md)，以便根据需要启用支持和添加存储。
