---
title: 将存储添加到 Azure HPC 缓存
description: 如何定义存储目标，以便 Azure HPC 缓存可以将本地 NFS 系统或 Azure Blob 容器用于长期文件存储
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: ab9b7fa330964f7db8393334dd8f209efd75573d
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611262"
---
# <a name="add-storage-targets"></a>添加存储目标

*存储目标* 是可通过 Azure HPC 缓存访问的文件的后端存储。 你可以添加 NFS 存储 (，例如本地硬件系统) ，或将数据存储在 Azure Blob 中。

最多可为一个缓存定义十个不同的存储目标。 缓存在一个聚合命名空间中显示所有存储目标。

添加存储目标后，会单独配置命名空间路径。 通常情况下，NFS 存储目标最多可以有10个命名空间路径，或更多为一些大配置。 有关详细信息，请参阅 [NFS 命名空间路径](add-namespace-paths.md#nfs-namespace-paths) 。

请记住，必须可以从缓存的虚拟网络中访问存储导出。 对于本地硬件存储，你可能需要设置一个 DNS 服务器，该服务器可以解析用于 NFS 存储访问的主机名。 在 [DNS 访问](hpc-cache-prerequisites.md#dns-access)中了解详细信息。

创建缓存后，添加存储目标。 按照以下过程操作：

1. [创建缓存](hpc-cache-create.md)
1. 在本文中定义存储目标 (信息) 
1. 为[聚合命名空间](hpc-cache-namespace.md)[创建面向客户端的路径](add-namespace-paths.md) () 

添加存储目标的过程略有不同，具体取决于是否要添加 Azure Blob 存储或 NFS 导出。 下面是每种情况的详细信息。

单击下面的图像，观看有关创建缓存并从 Azure 门户添加存储目标的 [视频演示](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) 。

[![视频缩略图： Azure HPC 缓存：安装程序 (单击以访问视频页面) ](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>添加新的 Azure Blob 存储目标

新的 Blob 存储目标需要空的 Blob 容器或使用 Azure HPC 缓存云文件系统格式的数据进行填充的容器。 详细了解如何在 [将数据移入 Azure blob 存储](hpc-cache-ingest.md)中预加载 Blob 容器。

"Azure 门户 **添加存储目标** " 页包括在添加新的 Blob 容器之前创建新的 Blob 容器的选项。

### <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，打开缓存实例，并单击左侧边栏中的 " **存储目标** "。

![设置 > 存储目标 "页的屏幕截图，表中有两个现有的存储目标，并在表上方的" + 添加存储目标 "按钮周围突出显示](media/add-storage-target-button.png)

" **存储目标** " 页将列出所有现有目标，并提供一个用于添加新目标的链接。

单击 " **添加存储目标** " 按钮。

!["添加存储目标" 页的屏幕截图，其中填充了新的 Azure Blob 存储目标的信息](media/hpc-cache-add-blob.png)

要定义 Azure Blob 容器，请输入此信息。

* **存储目标名称** -设置在 Azure HPC 缓存中标识此存储目标的名称。
* **目标类型** -选择 " **Blob**"。
* **存储帐户** -选择要使用的帐户。

  如 [添加访问角色](#add-the-access-control-roles-to-your-account)中所述，你将需要授权缓存实例访问存储帐户。

  有关可使用的存储帐户类型的信息，请参阅 [Blob 存储要求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* **存储容器** -选择此目标的 Blob 容器，或单击 " **新建**"。

  ![用于为新容器指定名称和访问级别 (专用) 的对话框屏幕截图](media/add-blob-new-container.png)

完成后，单击 **"确定"** 以添加存储目标。

> [!NOTE]
> 如果你的存储帐户防火墙设置为仅限 "所选网络" 的访问权限，请使用 [有关 Blob 存储帐户防火墙设置](hpc-cache-blob-firewall-fix.md)的解决方法中所述的临时解决方法。

### <a name="add-the-access-control-roles-to-your-account"></a>向你的帐户添加访问控制角色

Azure HPC 缓存使用 azure [RBAC)  (azure 基于角色的访问控制 ](https://docs.microsoft.com/azure/role-based-access-control/index) 来授权缓存服务访问 Azure Blob 存储目标的存储帐户。

存储帐户所有者必须为用户 "HPC 缓存资源提供程序" 显式添加角色 [存储帐户参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) 和 [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) 。

可以提前完成此操作，也可以通过单击页面上添加 Blob 存储目标的链接来完成此操作。 请记住，将角色设置传播到 Azure 环境可能需要长达五分钟，因此，在创建存储目标之前，请等待几分钟，然后再添加角色。

添加 Azure 角色的步骤：

1. 打开存储帐户 ** ("IAM) ** " 页上的 "访问控制"。  (" **添加存储目标** " 页中的链接会自动打开所选帐户的此页。 ) 

1. 单击 **+** 页面顶部的，然后选择 " **添加角色分配**"。

1. 从列表中选择 "存储帐户参与者" 角色。

1. 在 " **分配给** " 字段中，保留 ( "Azure AD 用户、组或服务主体" ) 所选的默认值。  

1. 在 **选择** 字段中，搜索 "hpc"。  此字符串应匹配一个名为 "HPC 缓存资源提供程序" 的服务主体。 单击该主体将其选中。

   > [!NOTE]
   > 如果搜索 "hpc" 不起作用，请尝试使用字符串 "storagecache"。 在 GA) 之前参与预览 (的用户可能需要使用服务主体的旧名称。

1. 单击底部的 " **保存** " 按钮。

1. 重复此过程以分配角色 "存储 Blob 数据参与者"。  

![添加角色分配 GUI 的屏幕截图](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>先决条件：存储帐户访问权限

添加 blob 存储目标之前，请检查缓存是否具有正确的角色以访问存储帐户，以及防火墙设置是否允许创建存储目标。

Azure HPC 缓存使用 azure [RBAC)  (azure 基于角色的访问控制 ](../role-based-access-control/index.yml) 来授权缓存服务访问 Azure Blob 存储目标的存储帐户。

存储帐户所有者必须为用户 "HPC 缓存资源提供程序" 显式添加角色 [存储帐户参与者](../role-based-access-control/built-in-roles.md#storage-account-contributor) 和 [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 。

如果缓存没有这些角色，则存储目标创建会失败。

角色设置可能需要长达五分钟的时间才能通过 Azure 环境传播，因此，在创建存储目标之前，请等待几分钟，然后再添加角色。

有关详细说明， [请参阅添加或删除使用 Azure CLI 的 Azure 角色分配](../role-based-access-control/role-assignments-cli.md) 。

同时检查存储帐户的防火墙设置。 如果防火墙设置为仅允许 "所选网络" 访问，则存储目标创建可能会失败。 使用 [有关 Blob 存储帐户防火墙设置](hpc-cache-blob-firewall-fix.md)的解决方法中所述的解决方法。

### <a name="add-a-blob-storage-target-with-azure-cli"></a>添加 blob 存储目标 Azure CLI

使用 [az hpc-缓存 blob-存储添加](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) 接口定义 Azure blob 存储目标。

> [!NOTE]
> Azure CLI 命令当前要求您在添加存储目标时创建命名空间路径。 这不同于与 Azure 门户接口一起使用的进程。

除了标准资源组和缓存名称参数，还必须为存储目标提供以下选项：

* ``--name`` -设置在 Azure HPC 缓存中标识此存储目标的名称。

* ``--storage-account``-帐户标识符，格式为：/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group><** account_name>*

  有关可使用的存储帐户类型的信息，请参阅 [Blob 存储要求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* ``--container-name`` -指定要用于此存储目标的容器的名称。

* ``--virtual-namespace-path`` -为此存储目标设置面向客户端的文件路径。 将路径用引号引起来。 请参阅 [计划聚合命名空间](hpc-cache-namespace.md) ，了解有关虚拟命名空间功能的详细信息。

示例命令：

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>添加新的 NFS 存储目标

NFS 存储目标具有不同于 Blob 存储目标的设置。 使用情况模型设置有助于缓存有效地缓存此存储系统中的数据。

![定义了 NFS 目标的 "添加存储目标" 页的屏幕截图](media/add-nfs-target.png)

> [!NOTE]
> 在创建 NFS 存储目标之前，请确保可从 Azure HPC 缓存访问存储系统并满足权限要求。 如果缓存无法访问存储系统，则存储目标创建将会失败。 有关详细信息，请阅读 [NFS 存储要求](hpc-cache-prerequisites.md#nfs-storage-requirements) 和 [排查 NAS 配置和 NFS 存储目标问题](troubleshoot-nas.md) 。

### <a name="choose-a-usage-model"></a>选择使用模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

创建指向 NFS 存储系统的存储目标时，需要为该目标选择使用模型。 此模型确定如何缓存数据。

有三个选项：

* **读取繁重的罕见写入** -如果想要加快对静态文件或很少更改的文件的读取访问权限，请使用此选项。

  此选项将缓存客户端读取的文件，但会立即将写入操作传递到后端存储。 存储在缓存中的文件永远不会与 NFS 存储卷上的文件进行比较。

  如果存在可能会在存储系统上直接修改文件而无需先将文件写入缓存的风险，请不要使用此选项。 如果发生这种情况，则永远不会用后端的更改更新文件的缓存版本，并且数据集可能会变得不一致。

* **写入次数超过 15%** -此选项可提高读写性能。 使用此选项时，所有客户端都必须通过 Azure HPC 缓存访问文件，而不是直接安装后端存储。 缓存的文件将包含不会存储在后端的最新更改。

  在此使用模式下，不会对照后端存储上的文件来检查缓存中的文件。 假定该文件的缓存版本是更高版本。 缓存中已修改的文件将在后端存储系统中写入一小时后写入后端存储系统，而无其他更改。

* **客户端向 NFS 目标写入会绕过缓存** -如果工作流中的任何客户端直接将数据写入存储系统，而无需先写入缓存，则选择此选项。 将缓存客户端请求的文件，但对客户端中的这些文件所做的任何更改都会立即传递回后端存储系统。

  使用此使用模式时，将经常对照后端版本检查缓存中的文件以获取更新。 此验证允许在缓存之外更改文件，同时保持数据一致性。

下表总结了使用模型的差异：

| 使用模型                   | 缓存模式 | 后端验证 | 最大写回延迟 |
|-------------------------------|--------------|-----------------------|--------------------------|
| 读取繁重的罕见写入 | 读取         | 从不                 | 无                     |
| 超过15% 写入       | 读取/写入   | 从不                 | 1 小时                   |
| 客户端绕过缓存      | 读取         | 30 秒            | 无                     |

### <a name="create-an-nfs-storage-target"></a>创建 NFS 存储目标

### <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，打开缓存实例，并单击左侧边栏中的 " **存储目标** "。

![设置 > 存储目标 "页的屏幕截图，表中有两个现有的存储目标，并在表上方的" + 添加存储目标 "按钮周围突出显示](media/add-storage-target-button.png)

" **存储目标** " 页将列出所有现有目标，并提供一个用于添加新目标的链接。

单击 " **添加存储目标** " 按钮。

![定义了 NFS 目标的 "添加存储目标" 页的屏幕截图](media/add-nfs-target.png)

为支持 NFS 的存储目标提供以下信息：

* **存储目标名称** -设置在 Azure HPC 缓存中标识此存储目标的名称。

* **目标类型** -选择 " **NFS**"。

* **主机名** -输入 NFS 存储系统的 IP 地址或完全限定的域名。 仅当缓存有权访问可解析名称的 DNS 服务器时，才 (使用域名 ) 

* **使用情况模型** -选择一个基于工作流的数据缓存配置文件，如 [选择上述使用情况模型](#choose-a-usage-model) 中所述。

完成后，单击 **"确定"** 以添加存储目标。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 Azure CLI 命令 [az hpc-cache nfs-target add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) 来创建存储目标。

> [!NOTE]
> Azure CLI 命令当前要求您在添加存储目标时创建命名空间路径。 这不同于与 Azure 门户接口一起使用的进程。

除缓存名称和缓存资源组外，还提供以下值：

* ``--name`` -设置在 Azure HPC 缓存中标识此存储目标的名称。
* ``--nfs3-target`` -你的 NFS 存储系统的 IP 地址。  (如果你的缓存有权访问可解析该名称的 DNS 服务器，则可以在此处使用完全限定的域名。 ) 
* ``--nfs3-usage-model`` -数据缓存配置文件之一，请参阅上面的 [选择使用情况模型](#choose-a-usage-model)中所述。

  使用命令 [az hpc-cache 用量-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list)验证使用模型的名称。

* ``--junction`` -接合参数使用存储系统上的导出路径链接面向客户端的虚拟文件路径。

  NFS 存储目标可以有多个虚拟路径，只要每个路径代表同一个存储系统上的不同导出或子目录。 在一个存储目标上创建一个存储系统的所有路径。

  你可以随时在存储目标上 [添加和编辑命名空间路径](add-namespace-paths.md) 。

  ``--junction``参数使用以下值：

  * ``namespace-path`` -面向客户端的虚拟文件路径
  * ``nfs-export`` -与面向客户端的路径关联的存储系统导出
  * ``target-path`` (可选) -导出的子目录（如果需要）

  示例：``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  阅读 [配置聚合命名空间](hpc-cache-namespace.md) ，了解有关虚拟命名空间功能的详细信息。

示例命令：

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

输出：
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="view-storage-targets"></a>查看存储目标

你可以使用 Azure 门户或 Azure CLI 来显示已为缓存定义的存储目标。

### <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，打开缓存实例，并单击左侧边栏上 "设置" 标题下的 " **存储目标**"。 "存储目标" 页列出了用于添加或删除它们的所有现有目标和控件。

单击存储目标的名称以打开其详细信息页。

有关详细信息，请参阅 [编辑存储目标](hpc-cache-edit-storage.md) 。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 [az hpc-缓存存储-目标列表](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) 选项来显示缓存的现有存储目标。 提供缓存名称和资源组 (，除非已将其全局设置) 。

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

使用 [az hpc-缓存存储-目标显示](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) 来查看有关特定存储目标的详细信息。  (按名称指定存储目标。 ) 

示例：

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>后续步骤

创建存储目标后，请继续执行这些任务以使缓存可供使用：

* [设置聚合命名空间](add-namespace-paths.md)
* [装载 Azure HPC 缓存](hpc-cache-mount.md)
* [将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)

如果需要更新任何设置，可以 [编辑存储目标](hpc-cache-edit-storage.md)。
