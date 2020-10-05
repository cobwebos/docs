---
title: 在虚拟网络中启用 Azure 机器学习工作室
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习工作室来访问存储在虚拟网络内部的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 58395463c494a95a8842cddbe4d51544ce03d212
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713367"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>在 Azure 虚拟网络中使用 Azure 机器学习工作室

本文介绍如何在虚拟网络中使用 Azure 机器学习工作室。 学习如何：

> [!div class="checklist"]
> - 从虚拟网络内部的资源访问工作室。
> - 授予工作室访问存储在虚拟网络内部的数据的权限。
> - 了解工作室如何影响存储安全性。

本文是由五部分组成的系列文章的第五部分，指导你如何保护 Azure 机器学习工作流。 强烈建议您通读第 [一部分： VNet 概述](how-to-network-security-overview.md) 以首先了解总体体系结构。 

请参阅本系列中的其他文章：

[1. VNet 概述](how-to-network-security-overview.md)  >  [2。保护工作区](how-to-secure-workspace-vnet.md)  >  [3。保护定型环境](how-to-secure-training-vnet.md)  >  [4。保护推断环境](how-to-secure-inferencing-vnet.md)  >  [5。启用 studio 功能](how-to-enable-studio-virtual-network.md)


> [!IMPORTANT]
> 尽管大多数工作室都可与虚拟网络中存储的数据配合使用，但集成笔记本并非如此。 集成笔记本不支持使用虚拟网络中的存储。 但你可以从计算实例使用 Jupyter Notebook。 有关详细信息，请参阅[访问计算实例笔记本中的数据]()部分。


## <a name="prerequisites"></a>先决条件

+ 阅读 [网络安全概述](how-to-network-security-overview.md) ，了解常见的虚拟网络方案和总体虚拟网络体系结构。

+ 要使用的预先存在的虚拟网络和子网。

+ 现有的[启用了专用链接的 Azure 机器学习工作区](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)。

+ 现有的[已添加虚拟网络的 Azure 存储帐户](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)。

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>从 VNet 内部的资源访问工作室

如果要从虚拟网络内的资源（例如，计算实例或虚拟机）访问工作室，则必须允许从虚拟网络到工作室的出站流量。 

例如，如果使用网络安全组 (NSG) 来限制出站流量，请将一条规则添加到__服务标记__目标 __AzureFrontDoor.Frontend__。

## <a name="access-data-using-the-studio"></a>使用工作室访问数据

[将 Azure 存储帐户添加到虚拟网络](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)后，你必须将你的存储帐户配置为使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)授予工作室对你的数据的访问权限。 Studio 支持配置为使用服务终结点或专用终结点的存储帐户。 默认情况下，存储帐户使用服务终结点。 若要启用存储的专用终结点，请参阅 [使用 Azure 存储的专用终结点](../storage/common/storage-private-endpoints.md)

如果未启用托管标识，则会收到以下错误 `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` 此外，将禁用以下操作：

* 预览工作室中的数据。
* 在设计器中将数据可视化。
* 提交 AutoML 试验。
* 启动标记项目。

工作室支持从虚拟网络中的以下数据存储类型读取数据：

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL 数据库

### <a name="configure-datastores-to-use-managed-identity"></a>将数据存储配置为使用托管标识

Azure 机器学习使用[数据存储](concept-data.md#datastores)连接到存储帐户。 使用以下步骤，将数据存储配置为使用托管标识。 

1. 在工作室中，选择“数据存储”。

1. 若要创建新的数据存储，请选择“+ 新建数据存储”。

    若要更新现有数据存储，请选择相应的数据存储并选择“更新凭据”。

1. 在数据存储设置中，对于“允许 Azure 机器学习服务使用工作区托管标识来访问存储”，选择“是” 。


这些步骤使用 Azure 基于资源的访问控制 (RBAC) 将工作区托管标识作为“读取者”添加到存储服务。 “读取者”访问权限允许工作区检索防火墙设置，并确保数据不会离开虚拟网络。

> [!NOTE]
> 这些更改可能需要长达 10 分钟才能生效。

## <a name="technical-notes-for-managed-identity"></a>托管标识的技术说明

使用托管标识访问存储服务会影响一些安全注意事项。 这些注意事项是专门针对你要访问的存储帐户类型的。 本部分介绍每种存储帐户类型的更改。

### <a name="azure-blob-storage"></a>Azure Blob 存储

对于 Azure Blob 存储，还会将工作区托管标识添加为 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)，以便它能够从 Blob 存储读取数据。

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 访问控制

你可以使用 RBAC 和 POSIX 样式的访问控制列表 (ACL) 来控制虚拟网络内的数据访问。

若要使用 RBAC，请将工作区托管标识添加到 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色。 有关详细信息，请参阅 [Azure 基于角色的访问控制](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control)。

若要使用 ACL，可以向工作区托管标识分配访问权限，就像向任何其他安全主体分配访问权限一样。 有关详细信息，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 访问控制

Azure Data Lake Storage Gen1 仅支持 POSIX 样式的访问控制列表。 可以像其他任何安全原则一样，将工作区管理的标识访问权限分配给资源。 有关详细信息，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。

### <a name="azure-sql-database-contained-user"></a>Azure SQL 数据库包含用户

若要使用托管标识访问存储在 Azure SQL 数据库中的数据，必须创建一个映射到托管标识的 SQL 包含用户。 若要详细了解如何从外部提供程序创建用户，请参阅[创建映射到 Azure AD 标识的包含用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。

创建 SQL 包含用户后，使用 [GRANT T-SQL 命令](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)向该用户授予权限。

### <a name="azure-machine-learning-designer-default-datastore"></a>Azure 机器学习设计器默认数据存储

默认情况下，该设计器使用附加到工作区的存储帐户来存储输出。 不过，可以指定它将输出存储到你有权访问的任何数据存储。 如果环境使用虚拟网络，你可以使用这些控制确保数据保持安全且可访问。

若要为管道设置新的默认存储，请执行以下操作：

1. 在管道草稿中，选择管道标题附近的“设置”齿轮图标。
1. 选择“选择默认数据存储”。
1. 指定新的数据存储。

还可以基于每个模块替代默认数据存储。 这使你可以控制每一单个模块的存储位置。

1. 选择要指定其输出的模块。
1. 展开“输出设置”部分。
1. 选择“替代默认输出设置”。
1. 选择“设置输出设置”。
1. 指定新的数据存储。

## <a name="next-steps"></a>后续步骤

本文是由四部分构成的虚拟网络系列文章中的可选部分。 若要了解如何保护虚拟网络，请参阅其余文章：

* [第1部分：虚拟网络概述](how-to-network-security-overview.md)
* [第 2 部分：保护工作区资源](how-to-secure-workspace-vnet.md)
* [第 3 部分：保护训练环境](how-to-secure-training-vnet.md)
* [第 4 部分：保护推理环境](how-to-secure-inferencing-vnet.md)