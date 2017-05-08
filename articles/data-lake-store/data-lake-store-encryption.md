---
title: "Azure Data Lake Store 中的加密 | Microsoft Docs"
description: "了解如何在 Azure Data Lake Store 中进行加密和密钥轮换"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: zh-cn
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Azure Data Lake Store 中的数据加密

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Azure Data Lake Store 中的加密概述

在 Azure Data Lake Store (ADLS) 中加密可以保护数据、实施企业安全策略以及满足合规性要求。 本文概述了如何通过设计让 Data Lake Store 实现数据加密，并讨论了技术方面的内容。

ADLS 支持默认启用静态数据透明加密。 下面是相关术语更详细一些的含义：

* 默认启用：创建新的 Azure Data Lake Store 帐户时，默认设置启用加密。 随后会按照惯例将存储在 Data Lake Store 中的数据加密，再将其存储到永久性介质上。 这是针对所有数据的行为，在创建帐户后不能更改。
* 透明：ADLS 会自动在存储之前加密数据，在检索之前解密数据。 加密由管理员在 Data Lake Store 级别配置和管理。 不会对数据访问 API 进行更改，因此不需在因加密而与 Data Lake Store 交互的应用程序和服务中更改。

此外，也会始终在 Data Lake Store 中对传输数据（也称移动数据）加密。 除了在存储到永久性介质之前对数据加密，还会始终通过 HTTPS（基于安全套接字层的 HTTP）对传输数据或移动数据进行保护。 HTTPS 是 Data Lake Store REST 接口唯一支持的协议。

![图 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>设置 Azure Data Lake Store 的加密

Azure Data Lake Store 的加密是在帐户创建过程中设置的，默认情况下始终启用。 客户可以选择自行管理密钥，也可以让 Azure Data Lake Store（默认）为其管理密钥。

若要了解如何设置 Azure Data Lake Store 的加密，请参阅 – [入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>揭秘 – 如何在 Azure Data Lake Store 中进行加密

### <a name="master-encryption-keys"></a>主加密密钥

Azure Data Lake Store 提供两种管理主加密密钥 (MEK) 的模式。 下面更详细地介绍了主加密密钥的用法。 现在，请假定主加密密钥为顶级密钥。 若要解密存储在 Data Lake Store 中的数据，必须访问主加密密钥。

管理主加密密钥的两种模式如下：

1.    服务管理的密钥
2.    客户管理的密钥

两种模式都是将主加密密钥存储在 Azure Key Vault 中进行保护。 Azure Key Vault 是 Azure 上的安全度很高的完全托管式服务，可以用来保护加密密钥的安全。 有关 Azure Key Vault 的详细信息，可阅读[此文](https://azure.microsoft.com/services/key-vault)

下面对两种 MEK 管理模式提供的功能进行了简要比较。

|  | 服务管理的密钥 | 客户管理的密钥 |
| --- | --- | --- |
|如何存储数据？|始终在存储前加密|始终在存储前加密|
|在何处存储主加密密钥？|Azure 密钥保管库|Azure 密钥保管库|
|是否可以在 Azure Key Vault 外部存储明文形式的加密密钥|否|否|
|能否检索 Azure Key Vault 中的 MEK？|不能。 存储在密钥保管库中以后，MEK 就只能用于加密和解密。|不会。 存储在密钥保管库中以后，MEK 就只能用于加密和解密。|
|谁拥有 Azure Key Vault 和 MEK？|Azure Data Lake Store 服务。|客户拥有 Azure Key Vault，后者属于客户自己的 Azure 订阅。 密钥保管库中的 MEK 可以通过软件或硬件 (HSM) 进行管理。|
|客户能否撤消对 Azure Data Lake Store 服务的 MEK 的访问权限？|否|是的。 客户可以管理 Azure Key Vault 的访问控制列表，删除 Azure Data Lake Store 服务的服务标识的访问控制项。|
|客户能否永久删除 MEK？|否|是的。 如果客户从 Azure Key Vault 中删除 MEK，则任何人（包括 Azure Data Lake Store 服务）都不能解密 ADLS 帐户中的数据。 <br><br> 如果客户在将 MEK 从 Azure Key Vault 中删除以前对其进行了显式备份，则可将其还原，然后对数据进行恢复。 但是，如果客户在将 MEK 从 Azure Key Vault 中删除以前未对其进行备份，则再也不能在以后解密 ADLS 帐户中的数据。|


除了顶级差异（即谁管理 MEK 及其所在的 Key Vault），该设计的其余部分对两种模式来说都是相同的。

选择主加密密钥的模式时，需要牢记一些与之相关的重要事项。

1.    预配 ADLS 帐户时，可以选择使用客户管理的密钥或 ADLS 管理的密钥
2.    一旦预配 ADLS 帐户，将无法更改模式

### <a name="encryption-and-decryption-of-data"></a>加密和解密数据

为 Azure Data Lake 设计数据加密时，使用三种类型的密钥。 下表总结了各类密钥的作用：

| 键                   | 缩写 | 关联项 | 存储位置                             | 类型       | 说明                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| 主加密密钥 | MEK          | ADLS 帐户 | Azure 密钥保管库                              | 非对称 | 可以由 ADLS 管理，也可以由客户管理                                                              |
| 数据加密密钥   | DEK          | ADLS 帐户 | 永久性存储 – 由 ADLS 服务管理 | 对称  | DEK 由 MEK 加密，加密的 DEK 由服务存储在永久性介质上 |
| 块加密密钥  | BEK          | 数据块 | 无                                         | 对称  | BEK 派生自 DEK 和数据块                                                      |

下图演示了这些概念：

![图 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>解密文件时所使用的伪算法：
1.    检查 ADLS 帐户的 DEK 是否已缓存并可供使用。
    * 否则，请从永久性存储读取加密的 DEK，然后将其发送到 Azure Key Vault 进行解密。 解密的 DEK 缓存到内存中后即可使用。
2.    对于文件中的每个数据块，请执行以下操作：
    * 从永久性存储读取加密的数据块
    * 根据 DEK 和加密的数据块生成 BEK
    * 使用 BEK 解密数据
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>加密数据块时使用的伪算法：
1.    检查 ADLS 帐户的 DEK 是否已缓存并可供使用。
    * 否则，请从永久性存储读取加密的 DEK，然后将其发送到 Azure Key Vault 进行解密。 解密的 DEK 缓存到内存中后即可使用。
2.    根据 DEK 为数据块生成唯一 BEK。
3.    使用 AES-256 加密通过 BEK 加密数据块。
4.    将加密的数据块存储在永久性存储上。

> [!NOTE] 
> 出于性能方面的考虑，可以将明文形式的数据加密密钥 (DEK) 临时缓存在内存中一段较短的时间，该时间一过即将其清除。 在永久性介质上，该密钥始终由主加密密钥 (MEK) 进行加密存储。

## <a name="key-rotation"></a>密钥轮换

Azure Data Lake Store 允许在使用客户管理的密钥时轮换主加密密钥 (MEK)。 若要了解如何使用客户管理的密钥设置 ADLS 帐户，请参阅[入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)页。

### <a name="pre-requisites"></a>先决条件

在设置 Azure Data Lake 帐户时，客户已选择使用其自己的密钥。 创建帐户后，无法更改此选项。 如果你使用默认选项进行加密，系统将始终使用 Azure Data Lake 管理的密钥来加密数据。在此选项中，客户不能轮换密钥，因为密钥由 Azure Data Lake 管理。 以下步骤假设你使用客户管理的密钥（从密钥保管库选择你自己的密钥）。

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>如何在 Azure Data Lake Store 中轮换密钥 (MEK)

1. 登录到新的 [Azure 门户](https://portal.azure.com/)
2. 导航到 Key Vault（用于存储与 Azure Data Lake Store 帐户关联的密钥），然后选择“密钥”。

    ![密钥](./media/data-lake-store-encryption/keyvault.png)

3.    选择与 Azure Data Lake Store 帐户关联的密钥，然后创建一个新版的该密钥。
  
   Azure Data Lake 目前只支持将密钥轮换成新版的密钥，不能轮换成其他密钥

   ![newversion](./media/data-lake-store-encryption/keynewversion.png)

4.    导航到 Azure Data Lake Storage 帐户，然后选择“加密”

    ![newversion](./media/data-lake-store-encryption/select-encryption.png)

5.    此时会出现一个告知你新版密钥可用的通知，以及一个用于将密钥轮换成该新版密钥的按钮。 单击“轮换密钥”即可将密钥更新为新版密钥。

    ![完成](./media/data-lake-store-encryption/rotatekey.png)

6. 此操作应该在不到 2 分钟内完成，且密钥轮换不会造成停机。 完成此操作后，可以看到新版密钥处于使用状态。

