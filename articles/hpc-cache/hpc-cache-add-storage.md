---
title: 将存储添加到 Azure HPC 缓存
description: 如何定义存储目标，以便 Azure HPC 缓存可以将本地 NFS 系统或 Azure Blob 容器用于长期文件存储
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: b10692e352007ee2b0fd18543d8ae2ad8f9819dc
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621466"
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

要定义 Azure Blob 容器，请输入此信息。

!["添加存储目标" 页的屏幕截图，其中填充了新的 Azure Blob 存储目标的信息](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles and also with correct search term -->

* **存储目标名称**-设置在 Azure HPC 缓存中标识此存储目标的名称。
* **目标类型**-选择 " **Blob**"。
* **存储帐户**-选择容器要引用的帐户。

  如[添加访问角色](#add-the-access-control-roles-to-your-account)中所述，你将需要授权缓存实例访问存储帐户。

  有关可使用的存储帐户类型的信息，请参阅[Blob 存储要求](hpc-cache-prereqs.md#blob-storage-requirements)。

* **存储容器**-选择此目标的 Blob 容器。

* **虚拟命名空间路径**-为此存储目标设置面向客户端的文件路径。 阅读[配置聚合命名空间](hpc-cache-namespace.md)，了解有关虚拟命名空间功能的详细信息。

完成后，单击 **"确定"** 以添加存储目标。

### <a name="add-the-access-control-roles-to-your-account"></a>向你的帐户添加访问控制角色

Azure HPC 缓存使用[基于角色的访问控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/index)来授权缓存应用程序访问 Azure Blob 存储目标的存储帐户。

存储帐户所有者必须为用户 "HPC 缓存资源提供程序" 显式添加角色[存储帐户参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)和[存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)。

可以提前完成此操作，也可以通过单击页面上添加 Blob 存储目标的链接来完成此操作。

添加 RBAC 角色的步骤：

1. 打开存储帐户的 "**访问控制（IAM）** " 页。 （"**添加存储目标**" 页中的链接会自动打开所选帐户的此页。）

1. 单击页面顶部的 " **+** ，然后选择"**添加角色分配**"。

1. 从列表中选择 "存储帐户参与者" 角色。

1. 在 "**将访问权限分配给**" 字段中，保留选定的默认值（"Azure AD 用户、组或服务主体"）。  

1. 在**选择**字段中，搜索 "hpc"。  此字符串应匹配一个名为 "HPC 缓存资源提供程序" 的服务主体。 单击该主体将其选中。

   > [!NOTE]
   > 如果搜索 "hpc" 不起作用，请尝试使用字符串 "storagecache"。 加入预览（GA 之前）的用户可能需要使用服务主体的旧名称。

1. 单击 "**保存**" 按钮，将角色分配添加到存储帐户。

1. 重复此过程以分配角色 "存储 Blob 数据参与者"。  

![添加角色分配 GUI 的屏幕截图](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>添加新的 NFS 存储目标

NFS 存储目标具有一些额外字段，可用于指定如何访问存储导出以及如何有效地缓存其数据。 此外，如果有多个可用的导出，则可以从一个 NFS 主机创建多个命名空间路径。

![定义了 NFS 目标的 "添加存储目标" 页的屏幕截图](media/hpc-cache-add-nfs-target.png)

为支持 NFS 的存储目标提供以下信息：

* **存储目标名称**-设置在 Azure HPC 缓存中标识此存储目标的名称。

* **目标类型**-选择 " **NFS**"。

* **主机名**-输入 NFS 存储系统的 IP 地址或完全限定的域名。 （仅当缓存有权访问可解析名称的 DNS 服务器时，才使用域名。）

* **使用情况模型**-选择一个基于工作流的数据缓存配置文件，如下所述[选择使用模型](#choose-a-usage-model)。

### <a name="nfs-namespace-paths"></a>NFS 命名空间路径

NFS 存储目标可以有多个虚拟路径，只要每个路径代表同一个存储系统上的不同导出或子目录。

创建一个存储目标的所有路径。
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

对于每个命名空间路径，请填写以下值：

* **虚拟命名空间路径**-为此存储目标设置面向客户端的文件路径。 阅读[配置聚合命名空间](hpc-cache-namespace.md)，了解有关虚拟命名空间功能的详细信息。

<!--  The virtual path should start with a slash ``/``. -->

* **Nfs 导出路径**-输入 NFS 导出的路径。

* **子目录路径**-如果要装入导出的特定子目录，请在此处输入。 否则，请将此字段留空。

完成后，单击 **"确定"** 以添加存储目标。

### <a name="choose-a-usage-model"></a>选择使用模型
<!-- referenced from GUI - update aka.ms link if you change this heading -->

创建指向 NFS 存储系统的存储目标时，需要为该目标选择*使用模型*。 此模型确定如何缓存数据。

* Read 繁重-如果你经常使用缓存来提高数据读取权限，请选择此选项。

* 读/写-如果客户端使用缓存进行读取和写入，请选择此选项。

* 客户端绕过缓存-如果客户端直接将数据写入存储系统，而无需先写入缓存，请选择此选项。

## <a name="next-steps"></a>后续步骤

创建存储目标之后，请考虑以下任务之一：

* [装载 Azure HPC 缓存](hpc-cache-mount.md)
* [将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)

如果需要更改存储目标，请阅读[编辑存储目标](hpc-cache-edit-storage.md)以了解操作方法。
