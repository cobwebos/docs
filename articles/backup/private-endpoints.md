---
title: 专用终结点
description: 了解为 Azure 备份创建专用终结点的过程，以及使用专用终结点有助于维护资源安全性的方案。
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: bc778506819c44291bb2d8f69cdd9ac0aed51399
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007851"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure 备份的专用终结点

使用 Azure 备份，可以安全地从恢复服务保管库（使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)）备份和还原数据。 专用终结点使用 VNet 中的一个或多个专用 IP 地址，从而将服务有效地引入 VNet。

本文将帮助你了解如何为 Azure 备份创建专用终结点的过程，以及使用专用终结点有助于维护资源安全性的方案。

## <a name="before-you-start"></a>开始之前

- 仅可为新的恢复服务保管库创建专用终结点（未向保管库注册任何项）。 因此必须先创建专用终结点，然后才能尝试保护保管库中的任何项。
- 一个虚拟网络可以包含多个恢复服务保管库的专用终结点。 此外，一个恢复服务保管库可以在多个虚拟网络中具有专用终结点。 但是，可为保管库创建的专用终结点的最大数目为12。
- 为保管库创建专用终结点后，保管库将被锁定。 除了包含保管库专用终结点的网络，无法从网络访问它（用于备份和还原）。 如果删除了保管库的所有专用终结点，则会从所有网络访问该保管库。
- 尽管 Azure 备份和 Azure Site Recovery 都使用了恢复服务保管库，但本文仅介绍如何将专用终结点用于 Azure 备份。
- Azure Active Directory 当前不支持私有终结点。 因此，当在 Azure Vm 中执行数据库的备份，并使用 MARS 代理进行备份时，需要允许 IPs 和 Fqdn Azure Active Directory 在区域中工作。 如果适用，还可以使用 NSG 标记和 Azure 防火墙标记允许 Azure AD 访问。
- 专用终结点不支持具有网络策略的虚拟网络。 在继续之前，需要禁用网络策略。

## <a name="recommended-and-supported-scenarios"></a>推荐和支持的方案

虽然为保管库启用了专用终结点，但它们仅用于在 Azure VM 中备份和还原 SQL 和 SAP HANA 工作负荷，并且仅限 MARS 代理备份。 还可以使用保管库来备份其他工作负荷（不过，它们不需要专用终结点）。 除了使用 MARS 代理备份 SQL 和 SAP HANA 工作负荷和备份之外，专用终结点还用于在 Azure VM 备份时执行文件恢复。 有关详细信息，请参阅下表：

| 在 Azure VM 中备份工作负荷（SQL、SAP HANA）、使用 MARS 代理备份 | 建议使用私有终结点，以允许进行备份和还原，而无需为虚拟网络提供 Azure 备份或 Azure 存储的任何 Ip/Fqdn。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM 备份**                                         | VM 备份不要求你允许访问任何 Ip 或 Fqdn。 因此，它不需要备份和还原磁盘的专用终结点。  <br><br>   但是，将包含专用终结点的保管库中的文件恢复限制为包含保管库专用终结点的虚拟网络。 <br><br>    如果使用 ACL'ed 非托管磁盘，请确保包含磁盘的存储帐户允许访问**受信任的 Microsoft 服务**（如果它是 ACL'ed）。 |
| **Azure 文件备份**                                      | Azure 文件备份存储在本地存储帐户中。 因此，不需要专用终结点来进行备份和还原。 |

## <a name="creating-and-using-private-endpoints-for-backup"></a>创建和使用私有终结点进行备份

本部分讨论在虚拟网络中创建和使用 Azure 备份的专用终结点时所涉及的步骤。

>[!IMPORTANT]
> 强烈建议您按照本文档中所述的顺序执行步骤。 如果不这样做，可能会导致保管库呈现为不兼容，以使用专用终结点，并要求你使用新的保管库重新启动此过程。

>[!NOTE]
> Azure 门户体验的某些元素当前可能不可用。 请参阅此类方案中的备用体验，直到你所在的区域完全可用。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

请参阅[此部分](#create-a-recovery-services-vault-using-the-azure-resource-manager-client)，了解如何使用 Azure 资源管理器客户端创建保管库。 这会创建一个已启用其托管标识的保管库。 [在此处](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)了解有关恢复服务保管库的详细信息。

## <a name="enable-managed-identity-for-your-vault"></a>为保管库启用托管标识

托管标识允许保管库创建和使用专用终结点。 本部分讨论如何为保管库启用托管标识。

1. 请参阅恢复服务保管库->**标识**。

    ![将标识状态更改为开启](./media/private-endpoints/identity-status-on.png)

1. 将**状态**更改为 **"打开**"，然后单击 "**保存**"。

1. 生成**对象 ID** ，该 ID 是保管库的托管标识。

    >[!NOTE]
    >启用后，不能禁用托管标识（即使暂时这样做）。 禁用托管标识可能导致不一致的行为。

## <a name="dns-changes"></a>DNS 更改

使用专用终结点需要专用 DNS 区域，以允许备份扩展将专用链接 Fqdn 解析到专用 Ip。 完全都需要三个专用 DNS 区域。 尽管必须 mandatorily 创建这些区域中的两个，但可以选择将第三个区域集成到专用终结点（创建专用终结点时），也可以单独创建。

你还可以使用自定义 DNS 服务器。 有关使用自定义 DNS 服务器的详细信息，请参阅[自定义 dns 服务器的 DNS 更改](#dns-changes-for-custom-dns-servers)。

### <a name="creating-mandatory-dns-zones"></a>创建强制 DNS 区域

需要创建两个必需的 DNS 区域：

- `privatelink.blob.core.windows.net`（适用于备份/还原数据）
- `privatelink.queue.core.windows.net`（适用于服务通信）

1. 在 "**所有服务**" 搜索栏中搜索**专用 DNS 区域**，然后从下拉列表中选择 "**专用 DNS 区域**"。

    ![选择专用 DNS 区域](./media/private-endpoints/private-dns-zone.png)

1. 在**专用 DNS 区域**"窗格中，单击" **+ 添加**"按钮开始创建新区域。

1. 在 "**创建专用 DNS 区域**" 窗格中，填写所需的详细信息。 订阅必须与创建专用终结点的位置相同。

    区域必须命名为：

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **区域**                           | **服务** | **订阅和资源组（RG）详细信息**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blob        | **订阅**：与**需要创建专用**终结点的位置相同，即 VNET 的 Rg 或专用终结点的 rg |
    | `privatelink.queue.core.windows.net` | 队列       | **Rg**： VNET 或专用终结点的 rg |

    ![创建专用 DNS 区域](./media/private-endpoints/create-private-dns-zone.png)

1. 完成后，请继续查看并创建 DNS 区域。

### <a name="optional-dns-zone"></a>可选 DNS 区域

客户可以选择将其专用终结点与 Azure 备份的专用 DNS 区域集成（在有关服务通信的创建专用终结点的部分中讨论）。 如果你不希望与专用 DNS 区域集成，则可以选择使用自己的 DNS 服务器或单独创建专用 DNS 区域。 这是上一节中讨论的两个必需的专用 DNS 区域的补充。

如果要在 Azure 中创建单独的专用 DNS 区域，可以使用用于创建必需 DNS 区域的相同步骤执行相同操作。 命名和订阅详细信息如下所示：

| **区域**                                                     | **服务** | **订阅和资源组详细信息**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **注意**：*地理*位置是指地区代码。 例如， *wcus*和*NE*分别用于美国中部和北欧。 | 备份      | **订阅** **：与需要创建专用**终结点的位置相同：订阅中的任何 RG |

请参阅[此列表](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)中的区域代码。

对于国家地域中的 URL 命名约定：

- [中国](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [德国](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)
- [US Gov](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>将专用 DNS 区域链接到你的虚拟网络

上面创建的 DNS 区域现在必须链接到要备份的服务器所在的虚拟网络。 需要为创建的所有 DNS 区域执行此操作。

1. 转到你在上一步中创建的 DNS 区域，然后导航到左侧栏上的 "**虚拟网络" 链接**。 出现后，单击 " **+ 添加**" 按钮
1. 填写所需的详细信息。 必须填写 "**订阅**" 和 "**虚拟网络**" 字段，其中包含服务器所在的虚拟网络的相应详细信息。 其他字段必须保留原样。

    ![添加虚拟网络链接](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>将权限授予保管库以创建所需的专用终结点

若要为 Azure 备份创建所需的专用终结点，保管库（保管库的托管标识）必须具有以下资源组的权限：

- 包含目标 VNet 的资源组
- 要在其中创建专用终结点的资源组
- 包含专用 DNS 区域的资源组

建议将这三个资源组的**参与者**角色授予保管库（托管标识）。 以下步骤介绍了如何针对特定的资源组执行此操作（需要为三个资源组中的每个资源组执行此操作）：

1. 转到资源组，并导航到左侧栏中的 "**访问控制（IAM）** "。
1. 进入**访问控制**后，请参阅**添加角色分配**。

    ![添加角色分配](./media/private-endpoints/add-role-assignment.png)

1. 在 "**添加角色分配**" 窗格中，选择 "**参与者**" 作为**角色**，并将保管库的**名称**用作**主体**。 选择保管库，并在完成后单击 "**保存**"。

    ![选择角色和主体](./media/private-endpoints/choose-role-and-principal.png)

若要以更精细的级别管理权限，请参阅[手动创建角色和权限](#create-roles-and-permissions-manually)。

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>创建和批准 Azure 备份的专用终结点

### <a name="creating-private-endpoints-for-backup"></a>创建用于备份的专用终结点

本部分介绍为保管库创建专用终结点的过程。

1. 在搜索栏中，搜索并选择 "**专用链接**"。 这会将你带到**专用链接中心**。

    ![搜索专用链接](./media/private-endpoints/search-for-private-link.png)

1. 在左侧导航栏上，单击 "**专用终结点**"。 在 "**专用终结点**" 窗格中，单击 " **+ 添加**" 开始为保管库创建专用终结点。

    ![在专用链接中心添加专用终结点](./media/private-endpoints/add-private-endpoint.png)

1. 进入 "**创建专用终结点**" 进程后，将需要指定用于创建专用终结点连接的详细信息。

    1. **基础知识**：填写专用终结点的基本详细信息。 该区域应与保管库和资源相同。

        ![填写基本详细信息](./media/private-endpoints/basic-details.png)

    1. **资源**：此选项卡要求您提到要为其创建连接的 PaaS 资源。 从所需订阅的资源类型中选择 " **microsoft.recoveryservices/保管库**"。 完成后，请选择恢复服务保管库的名称作为**资源**，并将**AzureBackup**作为**目标子资源**。

        ![填写资源选项卡](./media/private-endpoints/resource-tab.png)

    1. **配置**：在 "配置" 中，指定要在其中创建专用终结点的虚拟网络和子网。 这是 VM 所在的 Vnet。 你可以选择将**专用终结点**与专用 DNS 区域集成。 或者，也可以使用自定义 DNS 服务器或创建专用 DNS 区域。

        ![填写配置选项卡](./media/private-endpoints/configuration-tab.png)

    1. （可选）可以为专用终结点添加**标记**。

    1. 完成输入详细信息后，继续**查看并创建**。 验证完成后，单击 "**创建**" 以创建专用终结点。

## <a name="approving-private-endpoints"></a>批准专用终结点

如果创建专用终结点的用户也是恢复服务保管库的所有者，则将自动批准上面创建的专用终结点。 否则，保管库的所有者必须先批准专用终结点，然后才能使用该终结点。 本部分介绍如何通过 Azure 门户手动批准私有终结点。

请参阅[使用 azure 资源管理器客户端手动批准专用终结](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client)点，以使用 azure 资源管理器客户端批准专用终结点。

1. 在恢复服务保管库中，导航到左侧栏上的 "**专用终结点连接**"。
1. 选择要批准的专用终结点连接。
1. 在顶部栏中选择 "**批准**"。 如果要拒绝或删除终结点连接，还可以选择 "**拒绝**" 或 "**删除**"。

    ![批准专用终结点](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>添加 DNS 记录

>[!NOTE]
> 如果使用的是集成 DNS 区域，则不需要执行此步骤。 但是，如果你已创建自己的 Azure 专用 DNS 区域，或者使用的是自定义专用 DNS 区域，请确保按照此部分中所述的方式进行输入。

为保管库创建可选专用 DNS 区域和专用终结点后，需要将 DNS 记录添加到 DNS 区域。 可以手动执行此操作，也可以使用 PowerShell 脚本执行此操作。 只需对备份 DNS 区域执行此操作，Blob 和队列的系统就会自动更新。

### <a name="add-records-manually"></a>手动添加记录

这要求你将专用终结点中的每个 FQDN 的条目放入专用 DNS 区域。

1. 转到**专用 DNS 区域**，然后导航到左侧栏上的 "**概述**" 选项。 在此之后，单击 " **+ 记录集**" 以开始添加记录。

    ![选择 "+ 记录集" 以添加记录](./media/private-endpoints/select-record-set.png)

1. 在打开的 "**添加记录集**" 窗格中，为每个 FQDN 和专用 IP 添加一个条目作为 "**类型**" 记录。 Fqdn 和 Ip 列表可从专用终结点（在 "**概述**" 下）获得。 如以下示例中所示，专用终结点中的第一个 FQDN 将添加到专用 DNS 区域中的记录集。

    ![Fqdn 和 Ip 列表](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![添加记录集](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>使用 PowerShell 脚本添加记录

1. 启动 Azure 门户中的**Cloud Shell** ，然后在 PowerShell 窗口中选择 "**上传文件**"。

    ![选择 "在 PowerShell 中上传文件" 窗口](./media/private-endpoints/upload-file-in-powershell.png)

1. 上传此脚本： [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. 中转到你的主文件夹（例如： `cd /home/user` ）

1. 运行以下脚本：

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    以下是参数：

    - **订阅**：资源（保管库的专用终结点和专用 DNS 区域）所驻留的订阅
    - **vaultPEName**：为保管库创建的专用终结点的名称
    - **vaultPEResourceGroup**：包含保管库专用终结点的资源组
    - **dnsResourceGroup**：包含专用 DNS 区域的资源组
    - **Privatezone**：专用 DNS 区域的名称

## <a name="using-private-endpoints-for-backup"></a>使用私有终结点进行备份

在 VNet 中为保管库创建的专用终结点获得批准后，即可开始使用它们来执行备份和还原。

>[!IMPORTANT]
>在继续之前，请确保已成功完成文档中所述的所有步骤。 若要回顾，你必须已完成以下清单中的步骤：
>
>1. 已创建（新的）恢复服务保管库
>1. 已启用保管库以使用系统分配的托管标识
>1. 创建三个专用 DNS 区域（如果使用集成 DNS 区域进行备份，则两个）
>1. 已将专用 DNS 区域链接到 Azure 虚拟网络
>1. 已向保管库的托管标识分配相关权限
>1. 已为保管库创建专用终结点
>1. 已批准专用终结点（如果未自动批准）
>1. 已将所需的 DNS 记录添加到专用 DNS 区域以进行备份（仅适用于不使用集成专用 DNS 区域的情况）

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Azure VM 中的工作负荷的备份和还原（SQL、SAP HANA）

创建并批准专用终结点后，客户端不需要进行其他更改即可使用专用终结点。 所有从安全网络到保管库的通信和数据传输都将通过专用终结点执行。
但是，如果在已向保管库注册服务器（SQL/SAP HANA）后删除该保管库的专用终结点，则需要使用保管库重新注册该容器。 不需要停止对它们的保护。

### <a name="backup-and-restore-through-mars-agent"></a>通过 MARS 代理进行备份和还原

使用 MARS 代理备份本地资源时，请确保已将本地网络（包含要备份的资源）与包含保管库的专用终结点的 Azure VNet 对等互连，以便可以使用该终结点。 然后，你可以继续安装 MARS 代理并配置备份，如此处所述。 但是，必须确保仅通过对等互连网络进行备份的所有通信。

但是，如果在向保管库注册了 MARS 代理后删除该保管库的专用终结点，则需要使用保管库重新注册该容器。 不需要停止对它们的保护。

## <a name="additional-topics"></a>其他主题

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>使用 Azure 资源管理器客户端创建恢复服务保管库

你可以创建恢复服务保管库，并启用其托管标识（需要启用托管标识，如稍后将使用 Azure 资源管理器客户端进行查看）。 下面共享了用于执行此操作的示例：

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

上面的 JSON 文件应包含以下内容：

请求 JSON：

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

响应 JSON：

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>在此示例中通过 Azure 资源管理器客户端创建的保管库已使用系统分配的托管标识创建。

### <a name="managing-permissions-on-resource-groups"></a>管理资源组的权限

保管库的托管标识需要在将创建专用终结点的资源组和虚拟网络中具有以下权限：

- `Microsoft.Network/privateEndpoints/*`这对于在资源组中的专用终结点上执行 CRUD 是必需的。 应为资源组分配该资源组。
- `Microsoft.Network/virtualNetworks/subnets/join/action`在将专用 IP 附加到专用终结点的虚拟网络上，这是必需的。
- `Microsoft.Network/networkInterfaces/read`需要在资源组中获取为专用终结点创建的网络接口。
- 专用 DNS 区域参与者角色，此角色已存在并且可用于提供 `Microsoft.Network/privateDnsZones/A/*` 和 `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` 权限。

你可以使用以下方法之一创建具有所需权限的角色：

#### <a name="create-roles-and-permissions-manually"></a>手动创建角色和权限

创建以下 JSON 文件，并在该部分的末尾使用 PowerShell 命令来创建角色：

PrivateEndpointContributorRoleDef

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>使用脚本

1. 启动 Azure 门户中的**Cloud Shell** ，然后在 PowerShell 窗口中选择 "**上传文件**"。

    ![选择 "在 PowerShell 中上传文件" 窗口](./media/private-endpoints/upload-file-in-powershell.png)

1. 上传以下脚本： [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. 中转到你的主文件夹（例如： `cd /home/user` ）

1. 运行以下脚本：

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    以下是参数：

    - **订阅**： * * 具有要在其中创建保管库的专用终结点的资源组和将附加保管库专用终结点的子网

    - **vaultPEResourceGroup**：将在其中创建保管库的专用终结点的资源组

    - **vaultPESubnetResourceGroup**：将连接专用终结点的子网的资源组

    - **vaultMsiName**：保管库的 MSI 名称，与**VaultName**相同

1. 完成身份验证，该脚本将获取上面提供的给定订阅的上下文。 如果在租户中缺少适当的角色，并将角色分配给保管库的 MSI，则会创建相应的角色。

### <a name="creating-private-endpoints-using-azure-powershell"></a>使用 Azure PowerShell 创建专用终结点

#### <a name="auto-approved-private-endpoints"></a>自动批准的专用终结点

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>使用 Azure 资源管理器客户端手动批准专用终结点

1. 使用**GetVault**获取专用终结点的专用终结点连接 ID。

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    这会返回专用终结点连接 ID。 可以使用连接 ID 的第一部分检索连接的名称，如下所示：

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 从响应中获取**专用终结点连接 ID** （以及所需的**专用终结点名称**），并将其替换为以下 JSON 和 Azure 资源管理器 URI，并尝试将状态更改为 "批准/拒绝/断开连接"，如以下示例中所示：

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON：

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>自定义 DNS 服务器的 DNS 更改

#### <a name="create-dns-zones-for-custom-dns-servers"></a>为自定义 DNS 服务器创建 DNS 区域

需要创建三个专用 DNS 区域，并将其链接到虚拟网络。

| **区域**                                                     | **服务** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | 备份      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | 队列       |

>[!NOTE]
>在上面的文本中， *geo*指地区代码。 例如， *wcus*和*NE*分别用于美国中部和北欧。

请参阅[此列表](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)中的区域代码。

#### <a name="adding-dns-records-for-custom-dns-servers"></a>为自定义 DNS 服务器添加 DNS 记录

这要求你将专用终结点中的每个 FQDN 的条目放入专用 DNS 区域。

应注意的是，我们将使用为备份、Blob 和队列服务创建的专用终结点。

- 保管库的专用终结点使用创建专用终结点时指定的名称
- Blob 和队列服务的专用终结点的前缀与保管库的名称相同。

例如，下图显示了为名为*pee2epe*的专用终结点连接创建的三个专用终结点：

![专用终结点连接的三个专用终结点](./media/private-endpoints/three-private-endpoints.png)

备份服务的 DNS 区域（ `privatelink.<geo>.backup.windowsazure.com` ）：

1. 在**专用链接中心**导航到用于备份的专用终结点。 "概述" 页列出了专用终结点的 FQDN 和专用 Ip。

1. 为每个 FQDN 和专用 IP 分别添加一个条目作为 "类型" 记录。

    ![为每个 FQDN 和专用 IP 添加条目](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Blob 服务的 DNS 区域（ `privatelink.blob.core.windows.net` ）：

1. 在**专用链接中心**导航到 Blob 的专用终结点。 "概述" 页列出了专用终结点的 FQDN 和专用 Ip。

1. 添加 FQDN 和专用 IP 项作为一种类型记录。

    ![添加 FQDN 和专用 IP 项作为 Blob 服务的类型记录](./media/private-endpoints/add-type-a-record-for-blob.png)

队列服务的 DNS 区域（ `privatelink.queue.core.windows.net` ）：

1. 在**专用链接中心**导航到队列的专用终结点。 "概述" 页列出了专用终结点的 FQDN 和专用 Ip。

1. 添加 FQDN 和专用 IP 项作为一种类型记录。

    ![添加 FQDN 和专用 IP 项作为队列服务的类型记录](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>常见问题

Q. 能否为现有备份保管库创建专用终结点？<br>
A. 不能，只能为新备份保管库创建专用终结点。 因此，保管库决不能保护任何项目。 事实上，在创建专用终结点之前，不会尝试保护保管库中的任何项。

Q. 我尝试保护我的保管库中的某一项，但失败，并且保管库仍未包含任何受保护的项。 能否为此保管库创建专用终结点？<br>
A. 不能，保管库不能尝试在过去保护任何项。

Q. 我有一个使用专用终结点进行备份和还原的保管库。 以后是否可以添加或删除此保管库的专用终结点？<br>
A. 是的。 如果已为保管库和受保护的备份项创建了专用终结点，则可以在以后根据需要添加或删除专用终结点。

Q. Azure 备份的专用终结点是否也可用于 Azure Site Recovery？<br>
A. 不可以，用于备份的专用终结点仅可用于 Azure 备份。 如果服务支持，则需要为 Azure Site Recovery 创建新的专用终结点。

Q. 我错过了本文中的一个步骤，并对数据源进行保护。 是否仍可以使用专用终结点？<br>
A. 不遵循本文中的步骤并继续保护项目可能导致保管库无法使用专用终结点。 因此，建议您在继续保护项目之前引用此核对清单。

Q. 我是否可以使用我自己的 DNS 服务器，而不是使用 Azure 专用 DNS 区域或集成的专用 DNS 区域？<br>
A. 是的，你可以使用自己的 DNS 服务器。 但是，请确保按照此部分中的建议添加所有所需的 DNS 记录。

Q. 按照本文中的过程操作后，是否需要在服务器上执行任何其他步骤？<br>
A. 按照本文中详细说明的过程操作后，无需执行其他工作即可使用专用终结点进行备份和还原。

## <a name="next-steps"></a>后续步骤

- 了解[Azure 备份中的所有安全功能](security-overview.md)
