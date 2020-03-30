---
title: 将存储添加到 Azure HPC 缓存
description: 如何定义存储目标，以便 Azure HPC 缓存可以使用本地 NFS 系统或 Azure Blob 容器进行长期文件存储
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: rohogue
ms.openlocfilehash: a68bf06bad995f71bedf6a5bdedcb676737a8c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271884"
---
# <a name="add-storage-targets"></a>添加存储目标

*存储目标是*通过 Azure HPC 缓存实例访问的文件的后端存储。 您可以添加 NFS 存储（如本地硬件系统），或在 Azure Blob 中存储数据。

您最多可以为一个缓存定义十个不同的存储目标。 缓存在一个聚合命名空间中显示所有存储目标。

请记住，必须从缓存的虚拟网络访问存储导出。 对于本地硬件存储，您可能需要设置一个 DNS 服务器，该服务器可以解析主机名以进行 NFS 存储访问。 在[DNS 访问](hpc-cache-prereqs.md#dns-access)中阅读更多内容。

创建缓存后添加存储目标。 该过程略有不同，具体取决于是添加 Azure Blob 存储还是 NFS 导出。 每个的详细信息如下。

## <a name="open-the-storage-targets-page"></a>打开存储目标页面

从 Azure 门户打开缓存实例并单击左侧边栏上的**存储目标**。 存储目标页列出所有现有目标，并给出一个链接以添加新目标。

![侧边栏上的存储目标链接的屏幕截图，标题为"配置"，该标题介于类别标题"设置"和"监视"之间](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>添加新的 Azure Blob 存储目标

新的 Blob 存储目标需要一个空 Blob 容器或一个以 Azure HPC 缓存云文件系统格式填充数据的容器。 在[将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)中，阅读有关预加载 Blob 容器的更多内容。

您可以在添加新容器之前从此页面创建新容器。

要定义 Azure Blob 容器，请输入此信息。

![添加存储目标页的屏幕截图，该页面填充了新的 Azure Blob 存储目标的信息](media/hpc-cache-add-blob.png)

* **存储目标名称**- 设置在 Azure HPC 缓存中标识此存储目标的名称。
* **目标类型**- 选择**Blob**。
* **存储帐户**- 选择要使用的帐户。

  您需要授权缓存实例访问存储帐户，如[添加访问角色](#add-the-access-control-roles-to-your-account)中所述。

  有关可以使用的存储帐户类型的信息，请阅读[Blob 存储要求](hpc-cache-prereqs.md#blob-storage-requirements)。

* **存储容器**- 为此目标选择 Blob 容器，或单击"**创建新**"。

  ![用于为新容器指定名称和访问级别（私有）的对话框屏幕截图](media/add-blob-new-container.png)

* **虚拟命名空间路径**- 为此存储目标设置面向客户端的文件路径。 阅读[配置聚合命名空间](hpc-cache-namespace.md)以了解有关虚拟命名空间功能的更多内容。

完成后，单击"**确定"** 以添加存储目标。

> [!NOTE]
> 如果存储帐户防火墙设置为仅限制对"选定网络"的访问，请使用"[解决 Blob 存储帐户防火墙设置"](hpc-cache-blob-firewall-fix.md)中记录的临时解决方法。

### <a name="add-the-access-control-roles-to-your-account"></a>将访问控制角色添加到您的帐户

Azure HPC 缓存使用[基于角色的访问控制 （RBAC）](https://docs.microsoft.com/azure/role-based-access-control/index)授权缓存服务访问 Azure Blob 存储目标的存储帐户。

存储帐户所有者必须显式添加用户"HPC 缓存资源提供程序"的[存储帐户参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)和[存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)的角色。

您可以提前执行此操作，或者单击页面上添加 Blob 存储目标的链接。 请记住，角色设置最多可能需要五分钟才能在 Azure 环境中传播，因此在添加角色后应等待几分钟，然后再创建存储目标。

添加 RBAC 角色的步骤：

1. 打开存储帐户**的访问控制 （IAM）** 页面。 （**添加存储目标**页中的链接会自动打开所选帐户的此页面。

1. **+** 单击页面顶部的，然后选择 **"添加角色分配**"。

1. 从列表中选择"存储帐户参与者"角色。

1. 在"**分配对字段的访问**"中，保留默认值（"Azure AD 用户、组或服务主体"）。  

1. 在 **"选择"** 字段中，搜索"hpc"。  此字符串应匹配一个服务主体，称为"HPC 缓存资源提供程序"。 单击该主体以将其选中。

   > [!NOTE]
   > 如果搜索"hpc"不起作用，请尝试改用字符串"存储缓存"。 加入预览（在 GA 之前）的用户可能需要使用服务主体的旧名称。

1. 单击底部的 **"保存**"按钮。

1. 重复此过程以分配角色"存储 Blob 数据参与者"。  

![添加角色分配 GUI 的屏幕截图](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>添加新的 NFS 存储目标

NFS 存储目标具有比 Blob 存储目标多的字段。 这些字段指定如何访问存储导出以及如何高效地缓存其数据。 此外，如果 NFS 主机有多个导出可用，则 NFS 存储目标允许您创建多个命名空间路径。

![定义 NFS 目标的添加存储目标页的屏幕截图](media/hpc-cache-add-nfs-target.png)

为 NFS 支持的存储目标提供此信息：

* **存储目标名称**- 设置在 Azure HPC 缓存中标识此存储目标的名称。

* **目标类型**- 选择**NFS**。

* **主机名**- 输入 NFS 存储系统的 IP 地址或完全限定的域名。 （仅当缓存有权访问可以解析该名称的 DNS 服务器时，才使用域名。

* **使用情况模型**- 根据工作流选择一个数据缓存配置文件，下面[在选择使用模型](#choose-a-usage-model)中描述。

### <a name="nfs-namespace-paths"></a>NFS 命名空间路径

NFS 存储目标可以有多个虚拟路径，只要每个路径表示同一存储系统上的不同导出或子目录。

从一个存储目标创建所有路径。

您可以随时在存储目标上[添加和编辑命名空间路径](hpc-cache-edit-storage.md)。

为每个命名空间路径填写这些值：

* **虚拟命名空间路径**- 为此存储目标设置面向客户端的文件路径。 阅读[配置聚合命名空间](hpc-cache-namespace.md)以了解有关虚拟命名空间功能的更多内容。

<!--  The virtual path should start with a slash ``/``. -->

* **NFS 导出路径**- 输入 NFS 导出的路径。

* **子目录路径**- 如果要装载导出的特定子目录，请在此处输入它。 如果没有，请将此字段留空。

完成后，单击"**确定"** 以添加存储目标。

### <a name="choose-a-usage-model"></a>选择使用模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

当您创建指向 NFS 存储系统的存储目标时，您需要为该目标选择*使用模型*。 此模型确定数据的缓存方式。

有三个选项：

* **读取重写、不频繁的写入**- 如果要加快对静态或很少更改的文件的读取访问，请使用此选项。

  此选项缓存客户端读取的文件，但立即将写入传递到后端存储。 存储在缓存中的文件永远不会与 NFS 存储卷上的文件进行比较。

  如果存在文件可能直接在存储系统上被修改而不先写入缓存的风险，则不要使用此选项。 如果发生这种情况，该文件的缓存版本将永远不会更新与从后端的更改，并且数据集可能会变得不一致。

* **大于 15% 写入**- 此选项可加快读取和写入性能。 使用此选项时，所有客户端都必须通过 Azure HPC 缓存访问文件，而不是直接安装后端存储。 缓存的文件将具有未存储在后端的最新更改。

  在此使用模型中，不会对照后端存储上的文件检查缓存中的文件。 假定文件的缓存版本更最新。 缓存中修改后的文件仅在缓存中一小时后写入后端存储系统，并且没有其他更改。

* **客户端将写入 NFS 目标，绕过缓存**- 如果工作流中的任何客户端在不首先写入缓存的情况下将数据直接写入存储系统，则选择此选项。 客户端请求的文件将缓存，但从客户端对这些文件的任何更改将立即传回后端存储系统。

  使用此使用模型，会经常对照后端版本检查缓存中的文件以进行更新。 此验证允许在缓存外部更改文件，同时保持数据一致性。

下表总结了使用模型差异：

| 使用模型 | 缓存模式 | 后端验证 | 最大回写延迟 |
| ---- | ---- | ---- | ---- |
| 阅读重、不频繁的写入 | 读取 | 从不 | 无 |
| 大于 15% 写入 | 读取/写入 | 从不 | 1 小时 |
| 客户端绕过缓存 | 读取 | 30 秒 | 无 |

## <a name="next-steps"></a>后续步骤

创建存储目标后，请考虑以下任务之一：

* [装载 Azure HPC 缓存](hpc-cache-mount.md)
* [将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)

如果需要更新任何设置，可以[编辑存储目标](hpc-cache-edit-storage.md)。
