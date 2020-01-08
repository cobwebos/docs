---
title: 将存储添加到 Azure HPC 缓存
description: 如何定义存储目标，以便 Azure HPC 缓存可以将本地 NFS 系统或 Azure Blob 容器用于长期文件存储
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: rohogue
ms.openlocfilehash: 75d657fd9f3ee13c331450b324fd3b99e9cb6ca5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647200"
---
# <a name="add-storage-targets"></a>添加存储目标

*存储目标*是可通过 Azure HPC 缓存实例访问的文件的后端存储。 可以添加 NFS 存储（例如本地硬件系统），也可以将数据存储在 Azure Blob 中。

最多可为一个缓存定义十个不同的存储目标。 缓存在一个聚合命名空间中显示所有存储目标。

请记住，必须可以从缓存的虚拟网络中访问存储导出。 对于本地硬件存储，你可能需要设置一个 DNS 服务器，该服务器可以解析用于 NFS 存储访问的主机名。 在[DNS 访问](hpc-cache-prereqs.md#dns-access)中了解详细信息。

创建缓存后，添加存储目标。 此过程略有不同，具体取决于是否要添加 Azure Blob 存储或 NFS 导出。 下面是每种情况的详细信息。

## <a name="open-the-storage-targets-page"></a>打开 "存储目标" 页

在 Azure 门户中，打开缓存实例，并单击左侧边栏中的 "**存储目标**"。 "存储目标" 页将列出所有现有目标，并提供一个用于添加新目标的链接。

![边栏上的 "存储目标" 链接的屏幕截图，在 "配置" 标题下，这介于 "类别标题" 设置和 "监视" 之间](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>添加新的 Azure Blob 存储目标

新的 Blob 存储目标需要空的 Blob 容器或使用 Azure HPC 缓存云文件系统格式的数据进行填充的容器。 详细了解如何在[将数据移入 Azure blob 存储](hpc-cache-ingest.md)中预加载 Blob 容器。

可以在添加之前从此页面创建新的容器。

要定义 Azure Blob 容器，请输入此信息。

!["添加存储目标" 页的屏幕截图，其中填充了新的 Azure Blob 存储目标的信息](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles and also with correct search term -->

* **存储目标名称**-设置在 Azure HPC 缓存中标识此存储目标的名称。
* **目标类型**-选择 " **Blob**"。
* **存储帐户**-选择要使用的帐户。

  如[添加访问角色](#add-the-access-control-roles-to-your-account)中所述，你将需要授权缓存实例访问存储帐户。

  有关可使用的存储帐户类型的信息，请参阅[Blob 存储要求](hpc-cache-prereqs.md#blob-storage-requirements)。

* **存储容器**-选择此目标的 Blob 容器，或单击 "**新建**"。

  ![用于为新容器指定名称和访问级别（私有）的对话框屏幕截图](media/add-blob-new-container.png)

* **虚拟命名空间路径**-为此存储目标设置面向客户端的文件路径。 阅读[配置聚合命名空间](hpc-cache-namespace.md)，了解有关虚拟命名空间功能的详细信息。

完成后，单击 **"确定"** 以添加存储目标。

> [!NOTE]
> 如果你的存储帐户防火墙设置为仅限 "所选网络" 的访问权限，请使用[有关 Blob 存储帐户防火墙设置](hpc-cache-blob-firewall-fix.md)的解决方法中所述的临时解决方法。

### <a name="add-the-access-control-roles-to-your-account"></a>向你的帐户添加访问控制角色

Azure HPC 缓存使用[基于角色的访问控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/index)来授权缓存服务访问 Azure Blob 存储目标的存储帐户。

存储帐户所有者必须为用户 "HPC 缓存资源提供程序" 显式添加角色[存储帐户参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)和[存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)。

可以提前完成此操作，也可以通过单击页面上添加 Blob 存储目标的链接来完成此操作。 请记住，将角色设置传播到 Azure 环境可能需要长达五分钟，因此，在创建存储目标之前，请等待几分钟，然后再添加角色。

添加 RBAC 角色的步骤：

1. 打开存储帐户的 "**访问控制（IAM）** " 页。 （"**添加存储目标**" 页中的链接会自动打开所选帐户的此页。）

1. 单击页面顶部的 " **+** ，然后选择"**添加角色分配**"。

1. 从列表中选择 "存储帐户参与者" 角色。

1. 在 "**将访问权限分配给**" 字段中，保留选定的默认值（"Azure AD 用户、组或服务主体"）。  

1. 在**选择**字段中，搜索 "hpc"。  此字符串应匹配一个名为 "HPC 缓存资源提供程序" 的服务主体。 单击该主体将其选中。

   > [!NOTE]
   > 如果搜索 "hpc" 不起作用，请尝试使用字符串 "storagecache"。 加入预览（GA 之前）的用户可能需要使用服务主体的旧名称。

1. 单击底部的 "**保存**" 按钮。

1. 重复此过程以分配角色 "存储 Blob 数据参与者"。  

![添加角色分配 GUI 的屏幕截图](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>添加新的 NFS 存储目标

NFS 存储目标的字段比 Blob 存储目标多。 这些字段指定如何访问存储导出，以及如何有效缓存数据。 此外，如果 NFS 主机具有多个可用的导出功能，则 NFS 存储目标可创建多个命名空间路径。

![定义了 NFS 目标的 "添加存储目标" 页的屏幕截图](media/hpc-cache-add-nfs-target.png)

为支持 NFS 的存储目标提供以下信息：

* **存储目标名称**-设置在 Azure HPC 缓存中标识此存储目标的名称。

* **目标类型**-选择 " **NFS**"。

* **主机名**-输入 NFS 存储系统的 IP 地址或完全限定的域名。 （仅当缓存有权访问可解析名称的 DNS 服务器时，才使用域名。）

* **使用情况模型**-选择一个基于工作流的数据缓存配置文件，如下所述[选择使用模型](#choose-a-usage-model)。

### <a name="nfs-namespace-paths"></a>NFS 命名空间路径

NFS 存储目标可以有多个虚拟路径，只要每个路径代表同一个存储系统上的不同导出或子目录。

创建一个存储目标的所有路径。

你可以随时在存储目标上[添加和编辑命名空间路径](hpc-cache-edit-storage.md)。

对于每个命名空间路径，请填写以下值：

* **虚拟命名空间路径**-为此存储目标设置面向客户端的文件路径。 阅读[配置聚合命名空间](hpc-cache-namespace.md)，了解有关虚拟命名空间功能的详细信息。

<!--  The virtual path should start with a slash ``/``. -->

* **Nfs 导出路径**-输入 NFS 导出的路径。

* **子目录路径**-如果要装入导出的特定子目录，请在此处输入。 否则，请将此字段留空。

完成后，单击 **"确定"** 以添加存储目标。

### <a name="choose-a-usage-model"></a>选择使用模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

创建指向 NFS 存储系统的存储目标时，需要为该目标选择*使用模型*。 此模型确定如何缓存数据。

有三个选项：

* **读取繁重的罕见写入**-如果想要加快对静态文件或很少更改的文件的读取访问权限，请使用此选项。

  此选项将缓存客户端读取的文件，但会立即将写入操作传递到后端存储。 存储在缓存中的文件永远不会与 NFS 存储卷上的文件进行比较。

  如果存在可能会在存储系统上直接修改文件而无需先将文件写入缓存的风险，请不要使用此选项。 如果发生这种情况，则永远不会用后端的更改更新文件的缓存版本，并且数据集可能会变得不一致。

* **写入次数超过 15%** -此选项可提高读写性能。 使用此选项时，所有客户端都必须通过 Azure HPC 缓存访问文件，而不是直接安装后端存储。 缓存的文件将包含不会存储在后端的最新更改。

  在此使用模式下，不会对照后端存储上的文件来检查缓存中的文件。 假定该文件的缓存版本是更高版本。 缓存中已修改的文件仅在缓存中有一小时后写入后端存储系统，而无其他更改。

* **客户端向 NFS 目标写入会绕过缓存**-如果工作流中的任何客户端直接将数据写入存储系统，而无需先写入缓存，则选择此选项。 将缓存客户端请求的文件，但对客户端中的这些文件所做的任何更改都会立即传递回后端存储系统。

  使用此使用模式时，将经常对照后端版本检查缓存中的文件以获取更新。 此验证允许在缓存之外更改文件，同时保持数据一致性。

下表总结了使用模型的差异：

| 使用模型 | 缓存模式 | 后端验证 | 最大写回延迟 |
| ---- | ---- | ---- | ---- |
| 读取繁重的罕见写入 | 读取 | 从来没有 | 无 |
| 超过15% 写入 | 读取/写入 | 从来没有 | 1 小时 |
| 客户端绕过缓存 | 读取 | 30 秒 | 无 |

## <a name="next-steps"></a>后续步骤

创建存储目标之后，请考虑以下任务之一：

* [装载 Azure HPC 缓存](hpc-cache-mount.md)
* [将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)

如果需要更新任何设置，可以[编辑存储目标](hpc-cache-edit-storage.md)。
