---
title: 使用客户托管密钥加密 Azure HPC 缓存中的数据
description: 如何使用 Azure HPC 缓存 Azure Key Vault 来控制加密密钥的访问权限，而不是使用默认的 Microsoft 托管的加密密钥
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: v-erkel
ms.openlocfilehash: 2d10241b8395c33767ffeeb550d9d8060bde3ce3
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597733"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>将客户托管的加密密钥用于 Azure HPC 缓存

你可以使用 Azure Key Vault 来控制用于在 Azure HPC 缓存中加密数据的密钥的所有权。 本文介绍如何使用客户托管的密钥进行缓存数据加密。

> [!NOTE]
> 默认情况下，在 Azure 中存储的所有数据（包括在缓存磁盘上）都使用 Microsoft 托管密钥进行静态加密。 如果要管理用于对数据进行加密的密钥，则只需按照本文中的步骤进行操作。

此功能仅在 Azure HPC 缓存可用的某些 Azure 区域中提供。 有关详细信息，请参阅[区域可用性](hpc-cache-overview.md#region-availability)列表。

为 Azure HPC 缓存启用客户托管的密钥加密有三个步骤：

1. 设置用于存储密钥的 Azure Key Vault。
1. 创建 Azure HPC 缓存时，请选择 "客户管理的密钥加密"，并指定要使用的密钥保管库和密钥。
1. 创建缓存后，对其进行授权以访问密钥保管库。

在从新创建的缓存中对加密进行授权之前，不会完全设置加密（步骤3）。 这是因为必须将缓存的标识传递到密钥保管库，才能使其成为授权用户。 无法在创建缓存之前执行此操作，因为在创建缓存之前，该标识不存在。

创建缓存后，将无法在客户管理的密钥与 Microsoft 托管的密钥之间进行更改。 但是，如果缓存使用客户管理的密钥，则可以根据需要[更改](#update-key-settings)加密密钥、密钥版本和密钥保管库。

## <a name="understand-key-vault-and-key-requirements"></a>了解密钥保管库和密钥要求

Key vault 和密钥必须满足这些要求才能使用 Azure HPC 缓存。

Key vault 属性：

* **订阅**-使用用于缓存的同一订阅。
* **区域**-密钥保管库必须与 Azure HPC 缓存位于同一区域。
* **定价层**-标准层足以与 Azure HPC 缓存配合使用。
* **软删除**-如果尚未在密钥保管库上配置，Azure HPC 缓存会启用软删除。
* **清除保护**-必须启用清除保护。
* **访问策略**-默认设置已足够。
* **网络连接**-无论选择哪个终结点设置，Azure HPC 缓存都必须能够访问密钥保管库。

关键属性：

* **密钥类型**-RSA
* **RSA 密钥大小**-2048
* **已启用**-是

密钥保管库访问权限：

* 创建 Azure HPC 缓存的用户必须具有与[Key Vault 参与者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)等效的权限。 设置和管理 Azure Key Vault 需要相同的权限。

  有关详细信息，请阅读[对密钥保管库的安全访问](../key-vault/key-vault-secure-your-key-vault.md)。

## <a name="1-set-up-azure-key-vault"></a>1. 设置 Azure Key Vault

你可以在创建缓存之前设置密钥保管库和密钥，或者在创建缓存过程中执行此操作。 请确保这些资源满足[上述](#understand-key-vault-and-key-requirements)要求。

创建缓存时，必须指定用于缓存加密的保管库、密钥和密钥版本。

有关详细信息，请阅读[Azure Key Vault 文档](../key-vault/key-vault-overview.md)。

> [!NOTE]
> Azure Key Vault 必须使用相同的订阅，并且必须与 Azure HPC 缓存位于同一区域。 请确保所选的区域[支持客户管理的密钥功能](hpc-cache-overview.md#region-availability)。

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 创建缓存并启用客户托管密钥

创建 Azure HPC 缓存时，必须指定加密密钥源。 按照[创建 AZURE HPC 缓存](hpc-cache-create.md)中的说明进行操作，并在 "**磁盘加密密钥**" 页中指定密钥保管库和密钥。 可以在创建缓存期间创建新的 key vault 和密钥。

> [!TIP]
> 如果未显示 "**磁盘加密密钥**" 页，请确保缓存位于某个受支持的区域。

创建缓存的用户必须具有与[Key Vault 参与者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)相同或更高权限的权限。

1. 单击该按钮可启用私下管理的密钥。 更改此设置后，将显示密钥保管库设置。

1. 单击 "**选择密钥保管库**"，打开 "选择密钥" 页。 选择或创建密钥保管库和密钥以加密此缓存磁盘上的数据。

   如果 Azure Key Vault 未显示在列表中，请检查以下要求：

   * 缓存是否在与密钥保管库相同的订阅中？
   * 缓存是否位于与密钥保管库相同的区域中？
   * Azure 门户与密钥保管库之间是否存在网络连接？

1. 选择保管库后，从可用选项中选择单个密钥，或创建新密钥。 密钥必须是2048位的 RSA 密钥。

1. 指定所选密钥的版本。 在[Azure Key Vault 文档](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)中了解有关版本控制的详细信息。

继续使用其他规范并按照[创建 AZURE HPC 缓存](hpc-cache-create.md)中所述创建缓存。

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 从缓存授权 Azure Key Vault 加密
<!-- header is linked from create article, update if changed -->

几分钟后，新的 Azure HPC 缓存会出现在 Azure 门户中。 请访问 "**概述**" 页，授权其访问 Azure Key Vault，并启用客户管理的密钥加密。

> [!TIP]
> 缓存可能会出现在 "部署正在进行" 消息之前的 "资源" 列表中。 一分钟或两分钟后检查资源列表，而不是等待成功通知。

这两步过程是必需的，因为 Azure HPC 缓存实例需要标识才能传递到 Azure Key Vault 进行授权。 缓存标识直到其初始创建步骤完成后才会存在。

> [!NOTE]
> 创建缓存后，你必须在90分钟内授权加密。 如果未完成此步骤，缓存将超时并失败。 必须重新创建失败的缓存，无法对其进行修复。

缓存将显示 "**正在等待密钥**" 的状态。 单击页面顶部的 "**启用加密**" 按钮，授权缓存访问指定的密钥保管库。

![门户中缓存概述页的屏幕截图，突出显示 "启用加密" 按钮（顶部行）和状态：正在等待密钥](media/waiting-for-key.png)

单击 "**启用加密**"，然后单击 "**是"** 按钮，授权缓存使用加密密钥。 此操作还会在密钥保管库上启用软删除和清除保护（如果尚未启用）。

![门户中缓存概述页的屏幕截图，顶部有一个横幅消息，要求用户通过单击 "是" 启用加密](media/enable-keyvault.png)

缓存请求访问密钥保管库后，可以创建并加密存储缓存数据的磁盘。

授权加密之后，Azure HPC 缓存会经过几分钟的设置，以创建加密磁盘和相关的基础结构。

## <a name="update-key-settings"></a>更新密钥设置

可以从 Azure 门户更改缓存的密钥保管库、密钥或密钥版本。 单击缓存的 "**加密**设置" 链接，打开 "**客户密钥设置**" 页。

不能在客户管理的密钥和系统管理的密钥之间更改缓存。

!["客户密钥设置" 页的屏幕截图，通过单击 Azure 门户中 "缓存" 页上的 "设置" > 加密访问](media/change-key-click.png)

单击 "**更改密钥**" 链接，然后单击 "**更改密钥保管库、密钥或版本**" 以打开密钥选择器。

!["从 Azure Key Vault 中选择密钥" 页面的屏幕截图，其中三个下拉选择器用于选择密钥保管库、密钥和版本](media/select-new-key.png)

此缓存所在的同一订阅和区域中的密钥保管库将显示在列表中。

选择新的加密密钥值后，单击 "**选择**"。 此时会显示一个确认页，其中包含新值。 单击 "**保存**" 以完成选择。

![左上角的 "保存" 按钮的 "确认" 页的屏幕截图](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>阅读有关 Azure 中客户托管密钥的详细信息

这些文章详细介绍了如何使用 Azure Key Vault 和客户管理的密钥来加密 Azure 中的数据：

* [Azure 存储加密概述](../storage/common/storage-service-encryption.md)
* [使用客户管理的密钥的磁盘加密](../virtual-machines/linux/disk-encryption.md#customer-managed-keys)-有关将 Azure Key Vault 与托管磁盘配合使用的文档，这是 Azure HPC 缓存的类似方案

## <a name="next-steps"></a>后续步骤

创建 Azure HPC 缓存和基于授权的 Key Vault 加密后，通过向其授予对数据源的访问权限，继续设置缓存。

* [添加存储目标](hpc-cache-add-storage.md)
