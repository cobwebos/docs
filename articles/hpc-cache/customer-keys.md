---
title: 使用客户管理密钥加密 Azure HPC 缓存中的数据
description: 如何使用 Azure 密钥保管库与 Azure HPC 缓存一起控制加密密钥访问，而不是使用默认的 Microsoft 管理的加密密钥
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538864"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>使用客户管理的加密密钥进行 Azure HPC 缓存

可以使用 Azure 密钥保管库控制用于在 Azure HPC 缓存中加密数据的密钥的所有权。 本文介绍如何使用客户管理的密钥进行缓存数据加密。

> [!NOTE]
> 默认情况下，存储在 Azure 中的所有数据（包括在缓存磁盘上）均使用 Microsoft 管理的密钥进行静态加密。 如果想要管理用于加密数据的密钥，只需按照本文中的步骤操作。

此功能仅在以下 Azure 区域中可用：

* 美国东部
* 美国中南部
* 美国西部 2

为 Azure HPC 缓存启用客户管理密钥加密有三个步骤：

1. 设置 Azure 密钥保管库以存储密钥。
1. 创建 Azure HPC 缓存时，请选择客户管理的密钥加密，并指定要使用的密钥保管库和密钥。
1. 创建缓存后，授权它访问密钥保管库。

加密不会完全设置，直到您从新创建的缓存授权后（步骤 3）。 这是因为您必须将缓存的标识传递给密钥保管库，才能使其成为授权用户。 在创建缓存之前，不能执行此操作，因为在创建缓存之前，标识不存在。

创建缓存后，无法在客户管理的密钥和 Microsoft 管理的密钥之间进行更改。 但是，如果缓存使用客户管理的密钥，则可以根据需要[更改](#update-key-settings)加密密钥、密钥版本和密钥保管库。

## <a name="understand-key-vault-and-key-requirements"></a>了解密钥保管库和关键要求

密钥保管库和密钥必须满足这些要求才能使用 Azure HPC 缓存。

密钥保管库属性：

* **订阅**- 使用用于缓存的相同订阅。
* **区域**- 密钥保管库必须与 Azure HPC 缓存位于同一区域中。
* **定价层**- 标准层足以与 Azure HPC 缓存一起使用。
* **软删除**- Azure HPC 缓存将在密钥保管库中尚未配置软删除时启用软删除。
* **清除保护**- 必须启用清除保护。
* **访问策略**- 默认设置已足够。
* **网络连接**- Azure HPC 缓存必须能够访问密钥保管库，而不考虑您选择的终结点设置。

主要属性：

* **密钥类型**- RSA
* **RSA 密钥大小**- 2048
* **已启用**- 是

密钥保管库访问权限：

* 创建 Azure HPC 缓存的用户必须具有与[密钥保管库参与者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)等效的权限。 设置和管理 Azure 密钥保管库需要相同的权限。

  有关详细信息[，请阅读对密钥保管库的安全访问](../key-vault/key-vault-secure-your-key-vault.md)。

## <a name="1-set-up-azure-key-vault"></a>1. 设置 Azure 密钥保管库

您可以在创建缓存之前设置密钥保管库和密钥，也可以将其作为缓存创建的一部分进行。 确保这些资源符合[上述](#understand-key-vault-and-key-requirements)要求。

在创建缓存时，必须指定用于缓存加密的保管库、密钥和密钥版本。

有关详细信息，请阅读[Azure 密钥保管库文档](../key-vault/key-vault-overview.md)。

> [!NOTE]
> Azure 密钥保管库必须使用相同的订阅，并且与 Azure HPC 缓存位于同一区域。 使用本文开头列出的受支持区域之一。

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 在启用客户管理的密钥后创建缓存

创建 Azure HPC 缓存时，必须指定加密密钥源。 按照创建 Azure [HPC 缓存](hpc-cache-create.md)中的说明操作，并在 **"磁盘加密密钥"** 页中指定密钥保管库和密钥。 您可以在缓存创建期间创建新密钥保管库和密钥。

> [!TIP]
> 如果未显示 **"磁盘加密密钥"** 页，请确保缓存位于受支持的区域之一。

创建缓存的用户必须具有等于[密钥保管库参与者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)或更高权限的权限。

1. 单击该按钮可启用私有管理密钥。 更改此设置后，将显示密钥保管库设置。

1. 单击 **"选择密钥保管库**"以打开密钥选择页。 选择或创建用于加密此缓存磁盘上数据的密钥保管库和密钥。

   如果 Azure 密钥保管库未显示在列表中，请检查以下要求：

   * 缓存是否与密钥保管库处于同一订阅中？
   * 缓存是否与密钥保管库位于同一区域？
   * Azure 门户和密钥保管库之间是否有网络连接？

1. 选择保管库后，从可用选项中选择单个键，或创建新密钥。 密钥必须为 2048 位 RSA 密钥。

1. 指定所选密钥的版本。 在[Azure 密钥保管库文档中](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)了解有关版本控制的更多详细信息。

继续执行其余规范，并创建缓存，如创建 Azure [HPC 缓存](hpc-cache-create.md)中所述。

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 从缓存授权 Azure 密钥保管库加密
<!-- header is linked from create article, update if changed -->

几分钟后，新的 Azure HPC 缓存将显示在 Azure 门户中。 转到 **"概述"** 页以授权它访问 Azure 密钥保管库并启用客户管理的密钥加密。 （在清除"正在进行部署"消息之前，缓存可能会显示在资源列表中。

此两步过程是必需的，因为 Azure HPC 缓存实例需要一个标识才能传递到 Azure 密钥保管库以进行授权。 缓存标识在初始创建步骤完成之前不会存在。

> [!NOTE]
> 您必须在创建缓存后的 90 分钟内授权加密。 如果不完成此步骤，缓存将超时并失败。 必须重新创建失败的缓存，无法修复它。

缓存显示状态 **"等待键**"。 单击页面顶部的 **"启用加密**"按钮，授权缓存访问指定的密钥保管库。

![门户中缓存概览页的屏幕截图，在启用加密按钮（上一行）和状态：等待密钥上突出显示](media/waiting-for-key.png)

单击 **"启用加密**"，然后单击"**是**"按钮以授权缓存使用加密密钥。 此操作还支持密钥保管库上的软删除和清除保护（如果尚未启用）。

![门户中缓存概述页的屏幕截图，顶部有一条横幅消息，要求用户通过单击"是"来启用加密](media/enable-keyvault.png)

缓存请求访问密钥保管库后，它可以创建和加密存储缓存数据的磁盘。

授权加密后，Azure HPC 缓存将再经过几分钟的设置，以创建加密磁盘和相关基础结构。

## <a name="update-key-settings"></a>更新密钥设置

可以从 Azure 门户更改缓存的密钥保管库、密钥或密钥版本。 单击缓存的**加密**设置链接以打开 **"客户密钥设置"** 页。 （您不能在客户管理的密钥和系统管理的密钥之间更改缓存。

!["客户密钥设置"页的屏幕截图，通过单击 Azure 门户中缓存页的"设置>加密"到达](media/change-key-click.png)

单击 **"更改"键**链接，然后单击 **"更改密钥保管库、密钥或版本**"以打开密钥选择器。

!["从 Azure 密钥保管库中选择密钥"页的屏幕截图，其中有三个下拉选择器可选择密钥保管库、密钥和版本](media/select-new-key.png)

列表中将显示与此缓存相同的订阅和同一区域中的密钥保管库。

选择新的加密密钥值后，单击"**选择**"。 将显示一个确认页，其中显示新值。 单击 **"保存**"以完成所选内容。

![确认页面的屏幕截图，左上角有"保存"按钮](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>阅读有关 Azure 中客户管理密钥的基础知识

这些文章将介绍有关使用 Azure 密钥保管库和客户管理密钥加密 Azure 中的数据的介绍：

* [Azure 存储加密概述](../storage/common/storage-service-encryption.md)
* [使用客户管理的密钥进行磁盘加密](../virtual-machines/linux/disk-encryption.md#customer-managed-keys)- 使用 Azure 密钥保管库和托管磁盘的文档，这与 Azure HPC 缓存使用的过程类似

## <a name="next-steps"></a>后续步骤

创建 Azure HPC 缓存并授权基于密钥保管库的加密后，继续通过授予缓存对数据源的访问权限来设置缓存。

* [添加存储目标](hpc-cache-add-storage.md)
