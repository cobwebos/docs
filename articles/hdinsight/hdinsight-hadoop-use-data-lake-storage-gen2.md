---
title: 配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集
description: 了解如何使用 Azure HDInsight 群集上的 Azure 数据湖存储第 2 代。
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 4e8649096d4f7de49c9cf0d569422919f865bb3b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094086"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集

Azure 数据湖存储第 2 代是专用于大数据分析、 Azure Blob 存储上构建的云存储服务。 数据湖存储第 2 代将 Azure Blob 存储和 Azure 数据湖存储 Gen1 的功能相结合。 生成的服务可提供 Azure 数据湖存储 Gen1，如文件系统语义、 目录级别和文件级别的安全性和可伸缩性，以及成本较低的分层存储、 高可用性和灾难恢复功能的功能从 Azure Blob 存储。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

数据湖存储第 2 代是一个几乎所有的 Azure HDInsight 群集类型的默认值和其他存储帐户的存储选项可用。 但是，HBase，可以有一个数据湖存储第 2 代客户。

> [!Note] 
> 选择数据湖存储第 2 代为后你**主存储类型**，不能选择数据湖存储 Gen1 帐户作为附加存储。

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>创建包含数据湖存储第 2 代的 HDInsight 群集

## <a name="use-the-azure-portal"></a>使用 Azure 门户

若要创建数据湖存储第 2 代用于存储的 HDInsight 群集，请执行以下步骤来配置数据湖存储第 2 代帐户。

1. 创建用户分配的托管标识（如果还没有）。 请参阅[使用 Azure 门户创建、列出、删除用户分配的托管标识或为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

    ![创建用户分配的托管标识](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. 创建 Azure Data Lake Storage Gen2 存储帐户。 请确保**层次结构命名空间**选项处于启用状态。 有关详细信息，请参阅[快速入门：创建 Azure Data Lake Storage Gen2 存储帐户](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

    ![显示 Azure 门户中存储帐户创建情况的屏幕截图](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. 将托管标识分配到存储帐户上的“存储 Blob 数据所有者(预览版)”角色。 有关详细信息，请参阅[使用 RBAC 管理对 Azure Blob 和队列数据的访问权限（预览版）](../storage/common/storage-auth-aad-rbac.md)。

    1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。
    1. 选择你的存储帐户，然后选择**访问控制 (IAM)** 以显示该帐户的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。
    
        ![显示存储访问控制设置的屏幕截图](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. 选择 **+ 添加角色分配**按钮以添加新的角色。
    1. 在“添加角色分配”窗口中，选择“存储 Blob 数据所有者(预览版)”角色。 然后，选择具有托管标识和存储帐户的订阅。 接下来，搜索并找到之前创建的用户分配托管标识。 最后，选择托管的标识，并会在下列出**所选成员**。
    
        ![显示如何分配 RBAC 角色的屏幕截图](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
    1. 选择“保存”。 所选用户分配标识现在在“参与者”角色下列出。

    1. 此初始设置完成后，可通过门户创建群集。 群集必须与存储帐户位于同一 Azure 区域中。 在群集创建菜单的“存储”部分，选择以下选项：
        
        * 有关**主存储类型**，选择**Azure 数据湖存储第 2 代**。
        * 下**选择存储帐户**，搜索并选择新创建的数据湖存储第 2 代存储帐户。
        
            ![用于配合使用 Data Lake Storage Gen2 和 Azure HDInsight 的存储设置](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * 下**标识**、 选择正确的订阅和新创建的用户分配托管标识。
        
            ![用于配合使用 Data Lake Storage Gen2 和 Azure HDInsight 的标识设置](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

### <a name="use-an-azure-resource-manager-template-deployed-with-the-azure-cli"></a>使用 Azure 资源管理器模板使用 Azure CLI 部署

你可以[下载示例模板文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)并[下载示例参数文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 使用模板之前, 的字符串替换为`<SUBSCRIPTION_ID>`与实际的 Azure 订阅 id。 此外，替换字符串`<PASSWORD>`用您所选的密码设置的两个将用于登录到你的群集中的密码和 SSH 密码。

以下代码段将执行以下初始步骤操作：

1. 到 Azure 帐户中的日志。
1. 设置活动的订阅将完成其中的创建操作。
1. 创建名为的新部署活动的新资源组`hdinsight-deployment-rg`。
1. 创建名为用户分配托管的标识`test-hdinsight-msi`。
1. 将扩展添加到 Azure CLI，若要使用的数据湖存储第 2 代功能。
1. 创建一个名为的新数据湖存储第 2 代帐户`hdinsightadlsgen2`，通过使用`--hierarchical-namespace true`标志。

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接下来，登录到门户。 添加到新用户分配托管的标识**存储 Blob 数据参与者 （预览版）** 上的存储帐户，在步骤 3 中所述角色[使用 Azure 门户](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

分配用户分配托管标识的角色后，请使用以下代码片段来部署模板。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>数据湖存储第 2 代在 HDInsight 中的访问控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支持哪些类型的权限？

数据湖存储第 2 代使用支持基于角色的访问控制 (RBAC) 和类似的 POSIX 访问控制列表 (Acl) 的访问控制模型。 Data Lake 存储 Gen1 支持访问仅用于控制对数据的访问控制列表。

RBAC 使用角色分配来有效地对用户、 组和 Azure 资源的服务主体中应用的权限集。 通常情况下，这些 Azure 资源限制为顶级资源 （例如，Azure 存储帐户）。 Azure 存储，以及数据湖存储第 2 代，此机制已扩展到文件系统资源。

 使用 RBAC 的文件权限的详细信息，请参阅[Azure 基于角色的访问控制 (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

使用 Acl 的文件权限的详细信息，请参阅[对文件和目录访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何控制对数据湖存储第 2 代我的数据的访问

通过管理的标识控制访问数据湖存储第 2 代中的文件的 HDInsight 群集的能力。 托管的标识是 Azure Active Directory，(Azure AD) 的凭据由 Azure 管理中注册的标识。 管理的标识，不需要在 Azure AD 中注册服务主体或维护等证书凭据。

Azure 服务有两种类型的管理的标识： 系统分配和用户分配。 HDInsight 使用用户分配访问数据湖存储第 2 代为管理的标识。 用户分配的托管标识作为独立的 Azure 资源创建。 在创建过程中，Azure 会在由所用订阅信任的 Azure AD 租户中创建一个标识。 在创建标识后，可以将标识分配到一个或多个 Azure 服务实例。

用户分配标识的生命周期与它所分配到的 Azure 服务实例的生命周期是分开管理的。 有关托管标识的详细信息，请参阅[我如何为 Azure 资源的工作管理的标识？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何设置 Azure AD 用户的权限来查询数据的数据湖存储第 2 代中通过 Hive 或其他服务？

若要设置用户的权限来查询数据，请将 Azure AD 安全组用作在 Acl 中分配的主体。 不直接将文件访问权限分配给单个用户或服务主体中。 当使用 Azure AD 安全组来控制流的权限时，可以添加和删除用户或服务主体而不重新 Acl 应用到整个目录结构。 只需要从相应的 Azure AD 安全组添加或删除用户。 Acl 不会继承，因此，在重新应用 Acl 时需要更新上的每个文件和子目录的 ACL。

## <a name="next-steps"></a>后续步骤

* [将 Azure Data Lake Storage Gen2 预览版用于 Azure HDInsight 群集](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight 与 Data Lake Storage Gen2 预览版集成 - ACL 和安全更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 预览版简介](../storage/blobs/data-lake-storage-introduction.md)
