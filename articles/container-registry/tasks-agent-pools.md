---
title: 使用专用池运行任务-任务
description: 在注册表中设置专用的计算池 (代理池) ，以运行 Azure 容器注册表任务。
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions
ms.openlocfilehash: 86c539c3b34ca0e54d65f15c4d9d01a99f9b31c6
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997206"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>在专用代理池上运行 ACR 任务

设置 Azure 管理的 VM 池 (*代理池*) ，以便在专用计算环境中运行 [azure 容器注册表任务][acr-tasks] 。 在注册表中配置了一个或多个池之后，可以选择一个池来运行任务，以替代服务的默认计算环境。

代理池提供：

- **虚拟网络支持** -将代理池分配给 Azure vnet，以便能够访问 VNet 中的资源，例如容器注册表、密钥保管库或存储。
- **根据需要进行缩放** -为计算密集型任务增加代理池中的实例数，或将其扩展到零。 计费基于池分配。 有关详细信息，请参阅 [定价](https://azure.microsoft.com/pricing/details/container-registry/)。
- **灵活的选项** -从不同的 [池层](#pool-tiers) 中进行选择，并选择缩放选项以满足任务工作负荷需求。
- **Azure 管理** -任务池由 azure 进行修补和维护，提供保留的分配，无需维护单独的 vm。

此功能在“高级”容器注册表服务层级中可用。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表 SKU][acr-tiers]。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>

## <a name="preview-limitations"></a>预览版限制

- 任务代理池当前支持 Linux 节点。 当前不支持 Windows 节点。
- 可在以下区域的预览版中使用任务代理池：美国西部2、美国中南部、美国东部2、美国东部、美国中部、USGov 亚利桑那、USGov 德克萨斯州和 USGov 弗吉尼亚州。
- 对于每个注册表，默认的 total vCPU (核心) 配额对于所有标准代理池均为16，对于独立代理池为0。 为其他分配打开 [支持请求][open-support-ticket] 。
- 当前无法取消代理池上运行的任务。

## <a name="prerequisites"></a>先决条件

* 若要使用本文中 Azure CLI 的步骤，需要 Azure CLI 版本2.3.1 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。 或是在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行。
* 如果还没有容器注册表，请在预览区域) [创建一个][create-reg-cli] (高级层。

## <a name="pool-tiers"></a>池层

代理池层在池中为每个实例提供以下资源。

|层    | 类型  |  CPU  |内存 (GB)  |
|---------|---------|---------|---------|
|S1     |  standard    | 2       |    3     |
|S2     |  standard    | 4       |    8     |
|S3     |  standard    | 8       |   16     |
|I6     |  已隔离    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>创建和管理任务代理池

### <a name="set-default-registry-optional"></a>设置默认注册表 (可选) 

若要简化后面的 Azure CLI 命令，请通过运行 [az configure][az-configure] 命令设置默认注册表：

```azurecli
az configure --defaults acr=<registryName>
```

以下示例假定已设置了默认注册表。 如果不是，则 `--registry <registryName>` 在每个命令中传递参数 `az acr` 。

### <a name="create-agent-pool"></a>创建代理池

使用 [az acr agentpool create][az-acr-agentpool-create] 命令创建代理池。 以下示例创建一个 (4 CPU/实例) 的层 S2 池。 默认情况下，该池包含1个实例。

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> 创建代理池和其他池管理操作需要几分钟才能完成。

### <a name="scale-pool"></a>规模池

用 [az acr agentpool update][az-acr-agentpool-update] 命令向上或向下缩放池大小。 下面的示例将池缩放为2个实例。 可以缩放到0个实例。

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>在虚拟网络中创建池

### <a name="add-firewall-rules"></a>添加防火墙规则

任务代理池需要访问以下 Azure 服务。 必须将以下防火墙规则添加到任何现有的网络安全组或用户定义的路由。

| 方向 | 协议 | 源         | Source Port | 目标          | 目标端口 | 已使用    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| 出站  | TCP      | VirtualNetwork | 任意         | AzureKeyVault        | 443       | 默认值 |
| 出站  | TCP      | VirtualNetwork | 任意         | 存储              | 443       | 默认值 |
| 出站  | TCP      | VirtualNetwork | 任意         | EventHub             | 443       | 默认值 |
| 出站  | TCP      | VirtualNetwork | 任意         | AzureActiveDirectory | 443       | 默认值 |
| 出站  | TCP      | VirtualNetwork | 任意         | AzureMonitor         | 443       | 默认值 |

> [!NOTE]
> 如果任务需要公共 internet 上的其他资源，请添加相应的规则。 例如，需要其他规则来运行 docker 生成任务，该任务可从 Docker 中心提取基本映像，或还原 NuGet 包。

### <a name="create-pool-in-vnet"></a>在 VNet 中创建池

以下示例在网络*myvnet*的*mysubnet*子网中创建代理池：

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>在代理池上运行任务

下面的示例演示如何在对任务进行排队时指定代理池。

> [!NOTE]
> 若要在 ACR 任务中使用代理池，请确保该池至少包含1个实例。
>

### <a name="quick-task"></a>快速任务

使用 [az acr build][az-acr-build] 命令将快速任务排队，并传递 `--agent-pool` 参数：

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>自动触发的任务

例如，使用 [az acr task create][az-acr-task-create]在代理池中创建计划任务，传递 `--agent-pool` 参数。

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --commit-trigger-enabled false
```

若要验证任务设置，请运行 [az acr task run][az-acr-task-run]：

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>查询池状态

若要查找代理池中当前计划的运行数，请运行 [az acr agentpool show][az-acr-agentpool-show]。

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>后续步骤

有关云中的容器映像生成和维护的更多示例，请查看 [ACR 任务教程系列](container-registry-tutorial-quick-task.md)。



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md