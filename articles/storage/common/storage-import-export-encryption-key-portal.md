---
title: 使用 Azure 门户为导入/导出服务配置客户管理的密钥
description: 了解如何使用 Azure 门户使用 Azure 导入/导出服务的 Azure 密钥保管库配置客户管理密钥。 使用客户管理的密钥，你可以创建、轮换、禁用和撤销访问控制。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddcb47bfe8ba2b77efd8ff0aed52f1412107f0c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456492"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>在 Azure 密钥保管库中使用客户管理的密钥以进行导入/导出服务

Azure 导入/导出保护用于通过加密密钥锁定驱动器的 BitLocker 密钥。 默认情况下，BitLocker 密钥使用 Microsoft 管理的密钥进行加密。 为了对加密密钥进行其他控制，您还可以提供客户管理的密钥。

客户管理的密钥必须创建并存储在 Azure 密钥保管库中。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)

本文演示如何在[Azure 门户](https://portal.azure.com/)中使用客户管理的密钥以及导入/导出服务。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 您已按照以下说明创建了导入作业或导出作业：

    - [为 blob 创建导入作业](storage-import-export-data-to-blobs.md)。
    - [为文件创建导入作业](storage-import-export-data-to-files.md)。
    - [为 Blob 创建导出作业](storage-import-export-data-from-blobs.md)

2. 您有一个现有的 Azure 密钥保管库，其中有一个密钥，可用于保护 BitLocker 密钥。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure 密钥保管库设置和检索机密](../../key-vault/secrets/quick-create-portal.md)。

    - **软删除**和 **"不清除**"设置在现有的密钥保管库上。 默认情况下未启用这些属性。 要启用这些属性，请参阅以下文章中标题为 **"启用软删除**和**启用清除保护**"的部分：

        - [如何通过 PowerShell 使用软删除](../../key-vault/general/soft-delete-powershell.md)。
        - [如何通过 CLI 使用软删除](../../key-vault/general/soft-delete-cli.md)。
    - 现有密钥保管库应具有 2048 大小或以上的 RSA 密钥。 有关密钥的详细信息，请参阅["关于 Azure 密钥保管库密钥、机密和证书"](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**密钥保管库密钥**。
    - 密钥保管库必须与数据的存储帐户位于同一区域。  
    - 如果没有现有的 Azure 密钥保管库，也可以按照以下部分所述内联创建它。

## <a name="enable-keys"></a>启用密钥

为导入/导出服务配置客户管理的密钥是可选的。 默认情况下，导入/导出服务使用 Microsoft 托管密钥来保护 BitLocker 密钥。 若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 转到"**导入作业的概述"** 边栏选项卡。
2. 在右侧窗格中，**选择"选择 BitLocker 密钥的加密方式**"。

    ![选择加密选项](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. 在**加密**边栏选项卡中，您可以查看和复制设备 BitLocker 密钥。 在**加密类型**下，您可以选择如何保护 BitLocker 密钥。 默认情况下，使用 Microsoft 托管密钥。

    ![查看位锁键](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 您可以选择指定客户托管密钥。 选择客户托管密钥后，**选择密钥保管库和密钥**。

    ![选择客户托管密钥](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. 在**Azure 密钥保管库的"选择键**"选项卡中，将自动填充订阅。 对于**密钥保管库**，您可以从下拉列表中选择现有的密钥保管库。

    ![选择或创建 Azure 密钥保管库](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 您还可以选择 **"新建**"以创建新的密钥保管库。 在 **"创建密钥保管库"边栏选项卡**中，输入资源组和密钥保管库名称。 接受所有其他默认值。 选择“查看 + 创建”  。

    ![创建新的 Azure 密钥保管库](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 查看与密钥保管库关联的信息，然后选择 **"创建**"。 等待几分钟，完成密钥保管库创建。

    ![创建 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. 在**Azure 密钥保管库中选择密钥**中，可以在现有密钥保管库中选择密钥。

9. 如果创建新密钥保管库，请选择 **"新建**"以创建密钥。 RSA 密钥大小可以是 2048 或更高。

    ![在 Azure 密钥保管库中创建新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    如果在创建密钥保管库时未启用软删除和清除保护，则密钥保管库将更新为启用软删除和清除保护。

10. 提供密钥的名称，接受其他默认值，然后选择 **"创建**"。

    ![创建新键](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. 选择 **"版本**"，然后**选择"** 将通知您密钥在密钥保管库中创建。

    ![在密钥保管库中创建的新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

在 **"加密"** 边栏选项卡中，您可以看到为客户托管密钥选择的密钥保管库和密钥。

## <a name="disable-keys"></a>禁用密钥

您只能在导入/导出作业的任何阶段禁用 Microsoft 托管密钥并移动到客户托管密钥。 但是，创建客户托管密钥后，无法禁用该密钥。

## <a name="troubleshoot-customer-managed-key-errors"></a>排除客户托管的关键错误

如果您收到与客户托管密钥相关的任何错误，请使用下表进行故障排除：

| 错误代码     |详细信息     | 追 讨？    |
|----------------|------------|-----------------|
| CmkErrorAccess被吊销 | 应用了客户托管密钥，但密钥访问当前已吊销。 有关详细信息，请参阅如何[启用密钥访问](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)。                                                      | 是，检查是否： <ol><li>密钥保管库在访问策略中仍具有 MSI。</li><li>访问策略提供获取、包装、取消包装的权限。</li><li>如果密钥保管库位于防火墙后面的 vNet 中，请检查是否启用了**允许 Microsoft 受信任服务**。</li></ol>                                                                                            |
| CmkError 已禁用      | 应用了客户托管密钥，但该密钥已禁用。 有关详细信息，请参阅如何[启用密钥](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)。                                                                             | 是，通过启用密钥版本     |
| CmkError 未找到      | 应用了客户托管密钥，但找不到该密钥。 <br>如果在保留期后删除和清除密钥，则无法恢复该密钥。 如果备份了密钥，则可以还原密钥以解决此问题。 | 否，密钥已被删除，并在保留期后被清除。 <br>是，只有当客户有密钥备份并还原密钥时。  |
| CmkErrorVault 未找到 | 应用了客户托管密钥，但找不到与密钥关联的密钥保管库。<br>如果删除了密钥保管库，则无法恢复客户托管密钥。  如果将密钥保管库迁移到其他租户，请参阅在[订阅移动后更改密钥保管库租户 ID。](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix) |   否，如果客户已删除密钥保管库。<br> 是，如果密钥保管库进行了租户迁移，则执行以下操作之一： <ol><li>将密钥保管库移回旧租户。</li><li>设置标识 = 无，然后返回标识 = 系统分配，这将删除并重新创建标识</li></ol>|

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
