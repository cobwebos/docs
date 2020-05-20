---
title: 教程`:`使用托管标识访问 Azure Cosmos DB - Linux - Azure AD
description: 本教程将指导你完成使用 Linux VM 系统分配托管标识访问 Azure Cosmos DB 的过程。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f15a269656f205b0acb6a49740dd4c625c0bdd41
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "78248284"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>教程：使用 Linux VM 系统分配托管标识访问 Azure Cosmos DB 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


本教程介绍如何使用 Linux 虚拟机 (VM) 的系统分配托管标识访问 Azure Cosmos DB。 学习如何：

> [!div class="checklist"]
> * 创建 Cosmos DB 帐户
> * 在 Cosmos DB 帐户中创建集合
> * 向系统分配托管标识授予对 Azure Cosmos DB 实例的访问权限
> * 检索 Linux VM 的系统分配托管标识的 `principalID`
> * 获取访问令牌，并使用它调用 Azure 资源管理器
> * 从 Azure 资源管理器中获取访问密钥，以便进行 Cosmos DB 调用

## <a name="prerequisites"></a>必备条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

若要运行本教程中的 CLI 脚本示例，你有两种选择：

- 通过 Azure 门户或每个代码块右上角的“试用”按钮使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。 
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.23 或更高版本）。

## <a name="create-a-cosmos-db-account"></a>创建 Cosmos DB 帐户 

如果还没有 Cosmos DB 帐户，请创建一个。 可以跳过此步骤，使用现有的 Cosmos DB 帐户。 

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。 
2. 单击“数据库”，然后单击“Azure Cosmos DB”，新的“新建帐户”面板便会显示。  
3. 输入 Cosmos DB 帐户的 **ID**，供以后使用。  
4. **API** 应设置为“SQL”。 本教程中介绍的方法可以与其他可用的 API 类型配合使用，但本教程中的步骤是针对 SQL API 的。
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。    选择提供 Cosmos DB 的“位置”。 
6. 单击“创建”。 

## <a name="create-a-collection-in-the-cosmos-db-account"></a>在 Cosmos DB 帐户中创建集合

接下来，在 Cosmos DB 帐户中添加数据集合，以便在后续步骤中进行查询。

1. 导航到新创建的 Cosmos DB 帐户。
2. 在“概览”选项卡中单击“+/添加集合”按钮，此时“添加集合”面板就会滑出。  
3. 为集合提供数据库 ID、集合 ID，选择存储容量，输入分区键，输入吞吐量值，然后单击“确定”。   就本教程来说，使用“测试”作为数据库 ID 和集合 ID，选择固定的存储容量和最低吞吐量（400 RU/秒）就可以了。  

## <a name="retrieve-the-principalid-of-the-linux-vms-system-assigned-managed-identity"></a>检索 Linux VM 的系统分配托管标识的 `principalID`

若要在下一节中从资源管理器获得对 Cosmos DB 帐户访问密钥的访问，需要检索 Linux VM 的系统分配托管标识的 `principalID`。  请务必将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`（VM 所在的资源组）和 `<VM NAME>` 参数值替换为你自己的值。

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
响应包括系统分配托管标识的详细信息（请记下 principalID，因为下一部分中将使用它）：

```output  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>向 Linux VM 的系统分配托管标识授予对 Cosmos DB 帐户访问密钥的访问权限

Cosmos DB 原本不支持 Azure AD 身份验证。 但是，可以使用托管标识从资源管理器检索 Cosmos DB 访问密钥，然后使用该密钥访问 Cosmos DB。 在此步骤中，将向系统分配托管标识授予对 Cosmos DB 帐户密钥的访问权限。

若要向系统分配托管标识授予在 Azure 资源管理器中使用 Azure CLI 访问 Cosmos DB 帐户的权限，请更新环境的 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` 和 `<COSMOS DB ACCOUNT NAME>` 的值。 将 `<MI PRINCIPALID>` 替换为在“检索 Linux VM 的 MI 的 principalID”中由 `az resource show` 命令返回的 `principalId` 属性。  Cosmos DB 在使用访问密钥时支持两种级别的粒度：对帐户的读/写访问权限，以及对帐户的只读访问权限。  如果需要获取帐户的读/写密钥，请分配 `DocumentDB Account Contributor` 角色；如果需要获取帐户的只读密钥，请分配 `Cosmos DB Account Reader Role` 角色：

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

响应包括所创建的角色分配的详细信息：

```output
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>使用 Linux VM 的系统分配托管标识获取访问令牌并使用它来调用 Azure 资源管理器

至于本教程的剩余部分，请从先前创建的 VM 入手。

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/install_guide)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](../../virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](../../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 门户中，导航到“虚拟机”  ，转到 Linux 虚拟机，然后在“概述”  页中单击顶部的“连接”  。 复制用于连接到 VM 的字符串。 
2. 使用 SSH 客户端连接到 VM。  
3. 接下来，系统会提示你输入创建“Linux VM”时添加的“密码”。 然后应可以成功登录。  
4. 使用 CURL 获取 Azure 资源管理器的访问令牌： 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > 在上面的请求中，“resource”参数的值必须与 Azure AD 预期的值完全一致。 如果使用 Azure 资源管理器资源 ID，必须在 URI 的结尾添加斜线。
    > 在下面的响应中，为简洁起见，已缩短了 access_token 元素。
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>从 Azure 资源管理器中获取访问密钥，以便进行 Cosmos DB 调用  

现在，使用在上一部分中检索到的访问令牌并使用 CURL 来调用资源管理器，以便检索 Cosmos DB 帐户访问密钥。 有了访问密钥以后，即可查询 Cosmos DB。 请务必将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` 和 `<COSMOS DB ACCOUNT NAME>` 参数值替换为你自己的值。 将 `<ACCESS TOKEN>` 值替换为前面检索到的访问令牌。  若要检索读/写密钥，请使用密钥操作类型 `listKeys`。  若要检索只读密钥，请使用密钥操作类型 `readonlykeys`：

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> 上述 URL 中的文本区分大小写，因此如果对资源组使用了大小写格式，请务必在 URL 中相应地体现出来。 另外，请注意，这是 POST 请求而不是 GET 请求，请务必使用 -d 来传递一个值，以捕获长度限制，此值可以为 NULL。  

CURL 响应提供一个密钥列表。  例如，如果获取只读密钥：  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

有了 Cosmos DB 帐户的访问密钥以后，即可将其传递给 Cosmos DB SDK 并通过调用来访问该帐户。  如需快速示例，可将该访问密钥传递给 Azure CLI。  在 Azure 门户中，可以从 Cosmos DB 帐户边栏选项卡上的“概览”选项卡获取 `<COSMOS DB CONNECTION URL>`。   将 `<ACCESS KEY>` 替换为在上面获取的值：

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

此 CLI 命令返回有关集合的详细信息：

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Linux 虚拟机上的系统分配托管标识来访问 Cosmos DB。  若要详细了解 Cosmos DB，请参阅：

> [!div class="nextstepaction"]
>[Azure Cosmos DB 概述](/azure/cosmos-db/introduction)

