---
title: 配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集
description: 了解如何配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集。
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: fae92f8e09cc2ad6b63cb15599e0b1ab72588ed8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728837"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集

Azure Data Lake Storage (Data Lake Storage) Gen2 是一组专门用于大数据分析的功能，基于 Azure Blob 存储构建。 Data Lake Storage Gen2 是将 Azure Blob 存储和 Azure Data Lake Storage Gen1 的功能进行组合得到的结果。 获得的是提供 Azure Data Lake Storage Gen1 功能（例如文件系统语义、目录、文件级安全性和规模）与 Azure Blob 存储中的低成本分层存储、高可用性/灾难恢复功能的服务。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Azure Data Lake Storage Gen2 可用作几乎所有 Azure HDInsight 群集类型的存储选项，既可用作其默认值，也可用作其他存储帐户。 但是，HBase 只能拥有一个 Data Lake Storage Gen2 帐户。

> [!Note] 
> 选择 Data Lake Storage Gen2 作为主存储类型后，无法选择 Data Lake Storage Gen1 帐户作为附加存储。

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>创建包含 Data Lake Storage Gen2 的 HDInsight 群集

## <a name="using-the-azure-portal"></a>使用 Azure 门户

若要创建将 Data Lake Storage Gen2 用于存储的 HDInsight 群集，请使用以下步骤创建经正确配置的 Data Lake Storage Gen2 帐户。

1. 创建用户分配的托管标识（如果还没有）。 请参阅[使用 Azure 门户创建、列出、删除用户分配的托管标识或为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

    ![创建用户分配的托管标识](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. 创建 Azure Data Lake Storage Gen2 存储帐户。 确保已启用“分层文件系统”选项。 请参阅[快速入门：创建 Azure Data Lake Storage Gen2 存储帐户](../storage/blobs/data-lake-storage-quickstart-create-account.md)，获取更多详细信息。

    ![显示 Azure 门户中存储帐户创建情况的屏幕截图](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. 将托管标识分配到存储帐户上的“存储 Blob 数据所有者(预览版)”角色。 请参阅[使用 RBAC 管理对 Azure Blob 和队列数据的访问权限（预览版）](../storage/common/storage-auth-aad-rbac.md#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal)

    1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
    1. 选择存储帐户，然后选择“访问控制(IAM)”以显示该帐户的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。
    
        ![显示存储访问控制设置的屏幕截图](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. 单击“添加角色分配”按钮以添加一个新角色。
    1. 在“添加角色分配”窗口中，选择“存储 Blob 数据所有者(预览版)”角色。 然后，选择具有托管标识和存储帐户的订阅。 接下来，搜索并找到之前创建的用户分配托管标识。 最后，选择托管标识，它将在“选定成员”下列出。
    
        ![显示如何分配 RBAC 角色的屏幕截图](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
    1. 单击“ **保存**”。 所选用户分配标识现在在“参与者”角色下列出。

    1. 此初始设置完成后，可通过门户创建群集。 群集必须与存储帐户位于同一 Azure 区域中。 在群集创建菜单的“存储”部分，选择以下选项：
        
        * 对于“主存储类型”，单击“Azure Data Lake Storage Gen2”。
        * 在“选择存储帐户”下，搜索并选择新创建的 Data Lake Storage Gen2 存储帐户。
        
            ![用于配合使用 Data Lake Storage Gen2 和 Azure HDInsight 的存储设置](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * 在“标识”下，选择正确的订阅和新创建的用户分配托管标识。
        
            ![用于配合使用 Data Lake Storage Gen2 和 Azure HDInsight 的标识设置](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

### <a name="using-a-resource-manager-template-deployed-with-azure-cli"></a>使用通过 Azure CLI 部署的资源管理器模板

可[在此处下载示例模板文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)并[在此处下载示例参数文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 使用模板之前，用实际 Azure 订阅 ID 替换字符串 `<SUBSCRIPTION_ID>`。 此外，用所选的密码替换字符串 `<PASSWORD>`，以设置用于登录到群集的登录密码和 SSH 密码。

以下代码片段将执行下列初始步骤：

1. 登录到 Azure 帐户。
1. 设置将在其中执行创建操作的活动订阅。
1. 创建适用于新部署活动 `hdinsight-deployment-rg` 的新资源组。
1. 创建用户托管服务标识 (MSI) `test-hdinsight-msi`。
1. 将扩展添加到 Azure CLI，以使用 Data Lake Storage Gen2 的功能。
1. 使用 `--hierarchical-namespace true` 标志创建新的 Data Lake Storage Gen2 帐户 `hdinsightadlsgen2`。

```azurecli
az login
az account set --subscription <subscription_id>

#create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接下来，登录到门户，并将新的 MSI 添加到存储帐户上的“存储 Blob 数据参与者(预览)”角色，如上面[使用 Azure 门户](hdinsight-hadoop-use-data-lake-storage-gen2.md#using-the-azure-portal)下的步骤 3 所述。

在门户中完成 MSI 角色分配后，继续使用以下代码片段部署该模板。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中 Data Lake Storage Gen2 的访问控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支持哪些类型的权限？

Azure Data Lake Storage Gen2 实现了一个访问控制模型，该模型支持 Azure 基于角色的访问控制 (RBAC) 和像 POSIX 一样的访问控制列表 (ACL)。 Data Lake Storage Gen1 仅支持用于控制数据访问的访问控制列表。

Azure 基于角色的访问控制 (RBAC) 使用角色分配对用户、组和 Azure 资源的服务主体有效地应用权限集。 通常情况下，这些 Azure 资源限制为顶级资源（例如，Azure 存储帐户）。 对于 Azure 存储以及 Azure Data Lake Storage Gen2，此机制已扩展到文件系统资源。

 有关 RBAC 文件权限的详细信息，请参阅 [Azure 中基于角色的访问控制 (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

有关 ACL 文件权限的详细信息，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>如何在 Gen2 中控制对数据的访问？

HDInsight 群集在 Data Lake Storage Gen2 中访问文件的能力通过托管标识进行控制。 托管标识是在 Azure AD 中注册并由 Azure 管理其凭据的标识。 无需在 Azure AD 中注册服务主体，也无需维护证书等凭据。

Azure 服务有两种类型的托管标识：系统分配的托管标识和用户分配的托管标识。 Azure HDInsight 使用用户分配的托管标识来访问 Azure Data Lake Storage Gen2。 用户分配的托管标识作为独立的 Azure 资源创建。 在创建过程中，Azure 会在由所用订阅信任的 Azure AD 租户中创建一个标识。 在创建标识后，可以将标识分配到一个或多个 Azure 服务实例。 用户分配标识的生命周期与它所分配到的 Azure 服务实例的生命周期是分开管理的。 有关托管标识的详细信息，请参阅 [Azure 资源托管标识的工作原理](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>如何设置 Azure AD 用户的权限，以便使用 Hive 或其他服务在 Data Lake Storage Gen2 中查询数据？

将 Azure AD 安全组用作 ACL 中的分配主体。 不要直接给各个用户或服务主体分配文件访问权限。 使用 AD 安全组来控制权限流时，可以添加和删除用户或服务主体，而无需将 ACL 重新应用到整个目录结构。 只需要从相应的 Azure AD 安全组添加或删除用户。 ACL 不是继承的，因此，重新应用 ACL 需要更新每个文件和子目录上的 ACL。

## <a name="next-steps"></a>后续步骤

* [将 Azure Data Lake Storage Gen2 预览版用于 Azure HDInsight 群集](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight 与 Data Lake Storage Gen2 预览版集成 - ACL 和安全更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 预览版简介](../storage/blobs/data-lake-storage-introduction.md)
