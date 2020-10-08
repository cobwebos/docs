---
title: 将 Azure 订阅转移到不同的 Azure AD 目录
description: 了解如何将 Azure 订阅和已知相关资源转移到其他 Azure Active Directory (Azure AD) 目录。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 10/06/2020
ms.author: rolyon
ms.openlocfilehash: 35c6d94ce69acf59ae6cd8b26b0ad75645eb526a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819704"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>将 Azure 订阅转移到不同的 Azure AD 目录

组织可能具有多个 Azure 订阅。 每个订阅都与特定 Azure Active Directory (Azure AD) 目录相关联。 为了简化管理，你可能希望将订阅转移到其他 Azure AD 目录。 将订阅转移到其他 Azure AD 目录时，某些资源不会转移到目标目录。 例如，Azure 基于角色的访问控制 (Azure RBAC) 中的所有角色分配和自定义角色将从源目录中永久删除，不会转移到目标目录。

本文介绍将订阅转移到其他 Azure AD 目录并在转移后重新创建一些资源时可以遵循的基本步骤。

> [!NOTE]
> 对于 Azure 云解决方案提供程序 (CSP) 订阅，不支持更改订阅的 Azure AD 目录。

## <a name="overview"></a>概述

将 Azure 订阅转移到其他 Azure AD 目录是一个复杂的过程，必须仔细计划和执行。 许多 Azure 服务都需要安全主体（标识）才能正常运行，或者才能管理其他 Azure 资源。 本文将尽力涵盖很大程度上依赖于安全主体的大多数 Azure 服务，但这些服务并不全面。

> [!IMPORTANT]
> 在某些情况下，转移订阅的过程可能需要停机才能完成。 需要认真规划以评估转移是否需要停机。

下图显示了将订阅转移到其他目录时必须遵循的基本步骤。

1. 准备转移

1. 将 Azure 订阅转移到另一目录

1. 在目标目录中重新创建资源，例如角色分配、自定义角色和托管标识

    ![转移订阅示意图](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>决定是否将订阅转移到其他目录

以下是你可能想要转移订阅的一些原因：

- 由于公司合并或收购，你希望在主 Azure AD 目录中管理获得的订阅。
- 组织中的某个人员创建了一个订阅，你希望将管理合并到特定的 Azure AD 目录。
- 你的应用程序依赖于特定的订阅 ID 或 URL，并且无法轻松修改应用程序配置或代码。
- 部分业务已拆分为一个独立的公司，你需要将一些资源转移到其他 Azure AD 目录中。
- 出于安全隔离目的，你希望在不同的 Azure AD 目录中管理某些资源。

转移订阅的过程需要停机才能完成。 根据你的方案，最好重新创建资源并将数据复制到目标目录和订阅中。

### <a name="understand-the-impact-of-transferring-a-subscription"></a>了解转移订阅的影响

许多 Azure 资源都依赖于订阅或目录。 下表列出了转移订阅的已知影响，具体取决于你的情况。 通过执行本文中的步骤，你可以重新创建转移订阅之前存在的某些资源。

> [!IMPORTANT]
> 本部分列出了依赖于订阅的已知 Azure 服务或资源。 由于 Azure 中的资源类型在不断发展变化，可能还有其他没有列出的依赖项会对你的环境造成中断性变更。 

| 服务或资源 | 受影响 | 可恢复 | 你是否受到影响？ | 可执行的操作 |
| --------- | --------- | --------- | --------- | --------- |
| 角色分配 | “是” | “是” | [列出角色分配](#save-all-role-assignments) | 将永久删除所有角色分配。 必须将用户、组和服务主体映射到目标目录中的相应对象。 必须重新创建角色分配。 |
| 自定义角色 | “是” | “是” | [列出自定义角色](#save-custom-roles) | 将永久删除所有自定义角色。 必须重新创建自定义角色和任何角色分配。 |
| 系统分配的托管标识 | “是” | “是” | [列出托管标识](#list-role-assignments-for-managed-identities) | 必须禁用并重新启用托管标识。 必须重新创建角色分配。 |
| 用户分配的托管标识 | “是” | “是” | [列出托管标识](#list-role-assignments-for-managed-identities) | 必须删除、重新创建托管标识并将其附加到相应的资源。 必须重新创建角色分配。 |
| Azure Key Vault | “是” | “是” | [列出 Key Vault 访问策略](#list-key-vaults) | 必须更新与密钥保管库关联的租户 ID。 必须删除并添加新的访问策略。 |
| 启用了 Azure AD 身份验证集成的 Azure SQL 数据库 | 是 | 否 | [检查采用 Azure AD 身份验证的 Azure SQL 数据库](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Azure 存储和 Azure Data Lake Storage Gen2 | 是 | 是 |  | 必须重新创建任何 ACL。 |
| Azure Data Lake Storage Gen1 | “是” | “是” |  | 必须重新创建任何 ACL。 |
| Azure 文件 | 是 | 是 |  | 必须重新创建任何 ACL。 |
| Azure 文件同步 | 是 | 是 |  |  |
| Azure 托管磁盘 | 是 | 是 |  |  如果你使用磁盘加密集来使用客户管理的密钥来加密托管磁盘，则必须禁用并重新启用与磁盘加密集相关联的系统分配的标识。 您必须重新创建角色分配，即，对密钥保管库中的磁盘加密集再次授予所需权限。 |
| 用于 Kubernetes 的 Azure 容器服务 | 是 | 是 |  |  |
| Azure Active Directory 域服务 | 是 | 否 |  |  |
| 应用注册 | “是” | 是 |  |  |

> [!WARNING]
> 如果对依赖于密钥保管库的资源（例如存储帐户或 SQL 数据库）使用静态加密，而密钥保管库不位于正在转移的订阅中，则可能导致无法恢复的情况。 如果遇到这种情况，应采取步骤使用其他密钥保管库或暂时禁用客户管理的密钥，以避免这种不可恢复的情况。

## <a name="prerequisites"></a>先决条件

若要完成这些步骤，需要：

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) 或 [Azure CLI](/cli/azure)
- 要在源目录中转移的订阅的帐户管理员
- 目标目录中的[所有者](built-in-roles.md#owner)角色

## <a name="step-1-prepare-for-the-transfer"></a>步骤 1：准备转移

### <a name="sign-in-to-source-directory"></a>登录源目录

1. 以管理员身份登录 Azure。

1. 使用 [az account list](/cli/azure/account#az_account_list) 命令获取订阅列表。

    ```azurecli
    az account list --output table
    ```

1. 使用 [az account set](/cli/azure/account#az_account_set) 设置要转移的活动订阅。

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-resource-graph-extension"></a>安装 resource-graph 扩展

 借助 resource-graph 扩展，你可以使用 [az graph](/cli/azure/ext/resource-graph/graph) 命令来查询由 Azure 资源管理器管理的资源。 后续步骤中需要使用此命令。

1. 使用 [az extension list](/cli/azure/extension#az_extension_list) 查看是否安装了 resource-graph 扩展。

    ```azurecli
    az extension list
    ```

1. 如果没有安装，请安装 resource-graph 扩展。

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>保存所有角色分配

1. 使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) 列出所有角色分配（包括继承的角色分配）。

    为了更轻松地查看列表，可以将输出导出为 JSON、TSV 或表。 有关详细信息，请参阅[使用 Azure RBAC 和 Azure CLI 列出角色分配](role-assignments-list-cli.md)。

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. 保存角色分配的列表。

    转移订阅时，所有角色分配都将永久删除，因此保存副本非常重要。

1. 查看角色分配的列表。 可能存在目标目录中不需要的角色分配。

### <a name="save-custom-roles"></a>保存自定义角色

1. 使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list) 列出自定义角色。 有关详细信息，请参阅[使用 Azure CLI 创建或更新 Azure 自定义角色](custom-roles-cli.md)。

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. 将目标目录中需要的每个自定义角色另存为单独的 JSON 文件。

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. 创建自定义角色文件的副本。

1. 将每个副本修改为使用以下格式。

    稍后将使用这些文件在目标目录中重新创建自定义角色。

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>确定用户、组和服务主体映射

1. 根据你的角色分配列表，确定你将在目标目录中映射到的用户、组和服务主体。

    可以通过查看每个角色分配中的 `principalType` 属性来确定主体类型。

1. 如果需要，可以在目标目录中，创建所需的任何用户、组或服务主体。

### <a name="list-role-assignments-for-managed-identities"></a>列出托管标识的角色分配

将订阅转移到另一个目录时，不会更新托管标识。 因此，任何现有系统分配的或用户分配的托管标识将被破坏。 转移完成后，可以重新启用任何系统分配的托管标识。 对于用户分配的托管标识，必须重新创建并将它们附加到目标目录中。

1. 查看[支持托管标识的 Azure 服务列表](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)以了解可能用到托管标识的位置。

1. 使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 列出系统分配的和用户分配的托管标识。

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. 在托管标识列表中，确定哪些是系统分配的托管标识，哪些是用户分配的托管标识。 可以使用以下条件来确定类型。

    | 条件 | 托管标识类型 |
    | --- | --- |
    | `alternativeNames` 属性包括 `isExplicit=False` | 系统分配 |
    | `alternativeNames` 属性不包括 `isExplicit` | 系统分配 |
    | `alternativeNames` 属性包括 `isExplicit=True` | 用户分配 |

    还可以使用 [az identity list](/cli/azure/identity#az_identity_list) 命令仅列出用户分配的托管标识。 有关详细信息，请参阅[使用 Azure CLI 创建、列出或删除用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。

    ```azurecli
    az identity list
    ```

1. 获取托管标识的 `objectId` 值列表。

1. 搜索角色分配列表，以查看是否有托管标识的任何角色分配。

### <a name="list-key-vaults"></a>列出密钥保管库

创建密钥保管库时，它会自动绑定到创建它的订阅的默认 Azure Active Directory 租户 ID。 所有访问策略条目也都绑定到此租户 ID。 有关详细信息，请参阅[将 Azure Key Vault 移动到另一个订阅](../key-vault/general/move-subscription.md)。

> [!WARNING]
> 如果对依赖于密钥保管库的资源（例如存储帐户或 SQL 数据库）使用静态加密，而密钥保管库不位于正在转移的订阅中，则可能导致无法恢复的情况。 如果遇到这种情况，应采取步骤使用其他密钥保管库或暂时禁用客户管理的密钥，以避免这种不可恢复的情况。

- 如果有密钥保管库，请使用 [az keyvault show](/cli/azure/keyvault#az_keyvault_show) 列出访问策略。 有关详细信息，请参阅[分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-cli.md)。

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>列出采用 Azure AD 身份验证的 Azure SQL 数据库

- 使用 [az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) 和 [az graph](/cli/azure/ext/resource-graph/graph) 扩展来查看是否正在使用启用了 Azure AD 身份验证集成的 Azure SQL 数据库。 有关详细信息，请参阅[使用 SQL 配置和管理 Azure Active Directory 身份验证](../azure-sql/database/authentication-aad-configure.md)。

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>列出 ACL

1. 如果使用 Azure Data Lake Storage Gen1，请使用 Azure 门户或 PowerShell 列出应用到任何文件的 Acl。

1. 如果使用 Azure Data Lake Storage Gen2，请使用 Azure 门户或 PowerShell 列出应用到任何文件的 Acl。

1. 如果使用的是 Azure 文件，请列出应用于任何文件的 ACL。

### <a name="list-other-known-resources"></a>列出其他已知资源

1. 使用 [az account show](/cli/azure/account#az_account_show) 获取订阅 ID。

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. 使用 [az graph](/cli/azure/ext/resource-graph/graph) 扩展列出具有已知 Azure AD 目录依赖项的其他 Azure 资源。

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>步骤 2：转移订阅

在此步骤中，你需要将订阅从源目录转移到目标目录。 具体步骤取决于是否还要转移计费所有权。

> [!WARNING]
> 转移订阅时，源目录中的所有角色分配都将永久删除且无法还原。 订阅转移后无法取消。 执行此步骤之前，请确保已完成前面的步骤。

1. 确定是否还要将计费所有权转移到另一个帐户。

1. 将订阅转移到另一目录。

    - 若要保留当前计费所有权，请按照[将 Azure 订阅关联或添加到 Azure Active Directory 租户](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)中的步骤进行操作。
    - 如果还想要转让帐单所有权，请遵循将 [Azure 订阅的帐单所有权转移到另一个帐户](../cost-management-billing/manage/billing-subscription-transfer.md)中的步骤。 若要将订阅转移到不同的目录，必须选中 " **订阅 Azure AD 租户** " 复选框。

1. 完成订阅转移后，请返回到本文，了解如何在目标目录中重新创建资源。

## <a name="step-3-re-create-resources"></a>步骤 3：重新创建资源

### <a name="sign-in-to-target-directory"></a>登录目标目录

1. 在目标目录中，以接受转移请求的用户身份登录。

    只有新帐户中接受了转移请求的用户才有权管理这些资源。

1. 使用 [az account list](/cli/azure/account#az_account_list) 命令获取订阅列表。

    ```azurecli
    az account list --output table
    ```

1. 使用 [az account set](/cli/azure/account#az_account_set) 设置要使用的活动订阅。

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>创建自定义角色
        
- 使用 [az role definition create](/cli/azure/role/definition#az_role_definition_create) 从先前创建的文件中创建每个自定义角色。 有关详细信息，请参阅[使用 Azure CLI 创建或更新 Azure 自定义角色](custom-roles-cli.md)。

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>创建角色分配

- 使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 为用户、组和服务主体创建角色分配。 有关详细信息，请参阅[使用 Azure RBAC 和 Azure CLI 添加或删除角色分配](role-assignments-cli.md)。

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>更新系统分配的托管标识

1. 禁用并重新启用系统分配的托管标识。

    | Azure 服务 | 详细信息 | 
    | --- | --- |
    | 虚拟机 | [使用 Azure CLI 在 Azure VM 上配置 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | 虚拟机规模集 | [使用 Azure CLI 在虚拟机规模集上配置 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | 其他服务 | [支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. 使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 为系统分配的托管标识创建角色分配。 有关详细信息，请参阅[使用 Azure CLI 为托管标识分配对资源的访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>更新用户分配的托管标识

1. 删除、重新创建并附加用户分配的托管标识。

    | Azure 服务 | 详细信息 | 
    | --- | --- |
    | 虚拟机 | [使用 Azure CLI 在 Azure VM 上配置 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | 虚拟机规模集 | [使用 Azure CLI 在虚拟机规模集上配置 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | 其他服务 | [支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[使用 Azure CLI 创建、列出或删除用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. 使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 为用户分配的托管标识创建角色分配。 有关详细信息，请参阅[使用 Azure CLI 为托管标识分配对资源的访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>更新密钥保管库

本部分介绍更新密钥保管库的基本步骤。 有关详细信息，请参阅[将 Azure Key Vault 移动到另一个订阅](../key-vault/general/move-subscription.md)。

1. 将与订阅中的所有现有密钥保管库关联的租户 ID 更新到目标目录。

1. 删除所有现有的访问策略条目。

1. 添加与目标目录相关联的新访问策略条目。

### <a name="update-acls"></a>更新 ACL

1. 如果使用的是 Azure Data Lake Storage Gen1，请分配相应的 Acl。 有关详细信息，请参阅[保护 Azure Data Lake Storage Gen1 中存储的数据](../data-lake-store/data-lake-store-secure-data.md)。

1. 如果使用的是 Azure Data Lake Storage Gen2，请分配相应的 Acl。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../storage/blobs/data-lake-storage-access-control.md)。

1. 如果使用的是 Azure 文件存储，请分配相应的 ACL。

### <a name="review-other-security-methods"></a>查看其他安全方法

即使在转移过程中删除了角色分配，原始所有者帐户中的用户仍可以通过其他安全方法访问订阅，这些方法包括：

- 存储空间等服务的访问密钥。
- 用于向用户授予订阅资源管理员访问权限的[管理证书](../cloud-services/cloud-services-certs-create.md)。
- Azure 虚拟机等服务的远程访问凭据。

如果你打算删除源目录中用户的访问权限，以使他们在目标目录中没有访问权限，则应考虑轮换所有凭据。 转移之后，用户在凭据更新之前将继续具有访问权限。

1. 轮换存储帐户访问密钥。 有关详细信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)。

1. 如果将访问密钥用于其他服务（例如 Azure SQL 数据库或 Azure 服务总线消息传送），请轮换访问密钥。

1. 对于使用机密的资源，请打开资源设置并更新机密。

1. 对于使用证书的资源，请更新证书。

## <a name="next-steps"></a>后续步骤

- [将 Azure 订阅的计费所有权转移到另一帐户](../cost-management-billing/manage/billing-subscription-transfer.md)
- [在订阅者与 CSP 之间转移 Azure 订阅](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [将 Azure 订阅关联或添加到 Azure Active Directory 租户](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
