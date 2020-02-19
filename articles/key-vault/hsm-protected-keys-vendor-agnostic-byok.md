---
title: 如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥 - Azure Key Vault | Microsoft Docs
description: 使用这篇文章可帮助你规划、生成然后传输自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。 也称为 BYOK 或自带密钥。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: bd70cfb58c9d89f1d454537721e22f36b1fd3d3e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429288"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>将受 HSM 保护的密钥导入 Key Vault （预览版）

> [!NOTE]
> 此功能处于预览阶段，仅在**美国东部 2 EUAP**和**美国中部 EUAP**区域提供。 

若要在使用 Azure Key Vault 时添加保证，你可以在不离开 HSM 边界的硬件安全模块（Hsm）中导入或生成密钥。 这种情况通常被称为*自带密钥*，简称 BYOK。 Azure Key Vault 使用 Hsm （FIPS 140-2 Level 2）的 nCipher nShield 系列来保护密钥。

使用本主题中的信息，可帮助规划、生成并传输自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。

> [!NOTE]
> 此功能不适用于 Azure 中国世纪互联。 
> 
> 此 import 方法仅适用于[受支持的 hsm](#supported-hsms)。 

有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)  如需包括为受 HSM 保护的密钥创建密钥保管库的入门教程，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)。

## <a name="overview"></a>概述

* 在密钥保管库中生成密钥（称为密钥交换密钥或 KEK）。 这必须是使用 "import" 作为唯一密钥操作的 RSA HSM 密钥。 仅密钥保管库高级 SKU 支持 RSA-HSM 密钥。
* 下载 KEK 作为 pem 文件的公钥
* 将 KEK 公钥传输到连接到本地 HSM 的脱机工作站。
* 从脱机工作站使用 HSM 供应商提供的 BYOK 工具来创建 BYOK 文件。 
* 使用 KEK 加密目标密钥，该密钥将保持加密状态，直到将其传输到 Azure Key Vault 的 Hsm。 只有已加密版本的密钥才能离开本地 HSM。
* 在 Azure Key Vault Hsm 内生成的 KEK 不可导出。 Hsm 强制实施 Key Vault Hsm 外，不能有明确的 KEK 版本。
* KEK 必须位于要导入目标密钥的同一密钥保管库中。
* 将 BYOK 文件上传到 Key Vault 时，Key Vault Hsm 使用 KEK 私钥来解密目标密钥材料，并将其作为 HSM 密钥导入。 此操作完全在 Key Vault Hsm 内发生，并且目标密钥始终保留在 HSM 保护边界内。

## <a name="prerequisites"></a>必备条件

有关适用于 Azure 密钥保管库的“自带密钥 (BYOK)”的先决条件列表，请参阅下表。

| 要求 | 详细信息 |
| --- | --- |
| Azure 订阅 |若要创建 Azure 密钥保管库，需要 Azure 订阅：[注册免费试用版](https://azure.microsoft.com/pricing/free-trial/) |
| 密钥保管库（高级 SKU），用于导入受 HSM 保护的密钥 |请参阅 [Azure 密钥保管库定价](https://azure.microsoft.com/pricing/details/key-vault/)网站，了解有关 Azure 密钥保管库的服务层级和功能的详细信息。 |
| 受支持的 hsm 列表中的 HSM 以及 HSM 供应商提供的 BYOK 工具和说明 | 您必须有权访问硬件安全模块和 Hsm 的基本操作知识。 请参阅[支持的 hsm](#supported-hsms)。 |
| Azure CLI 版本2.0.82 或更高版本 | 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 。|

## <a name="supported-hsms"></a>支持的 Hsm

|HSM 供应商名称|支持的 HSM 模型|其他详细信息|
|---|---|---|
|Thales|SafeNet Luna HSM 7 系列固件版本7.3 或更高版本| [SafeNet Luna BYOK 工具和文档](https://safenet.gemalto.com/blah-blah)|


> [!NOTE]
> 若要从 Hsm 的 nCipher nShield 系列导入受 HSM 保护的密钥，[请使用旧版 BYOK 过程](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>生成密钥并将其传输到 Azure 密钥保管库 HSM

你将使用以下步骤生成密钥并将其传输到 Azure Key Vault HSM：

* [步骤1：生成 KEK](#step-1-generate-a-kek)
* [步骤2：下载 KEK 公钥](#step-2-download-kek-public-key)
* [步骤3：生成并准备要传输的密钥](#step-3-generate-and-prepare-your-key-for-transfer)
* [步骤4：将密钥传输到 Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>步骤1：生成 KEK

KEK （密钥交换密钥）是 Key Vault 的 HSM 中生成的 RSA 密钥。 此密钥用于对要导入的密钥（目标密钥）进行加密。

KEK 必须是：
1. **RSA-HSM**密钥（2048位或3072位或4096位）
2. 在你打算导入目标密钥的同一密钥保管库中生成
3. 已使用允许的密钥操作设置为**导入**创建

使用[az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令创建 KEK，并将关键操作设置为 "导入"。 记下从以下命令返回的密钥标识符 "儿童"。 你将在[步骤 3](#step-3-generate-and-prepare-your-key-for-transfer)中需要它。


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>步骤2：下载 KEK 公钥

使用[az keyvault key 下载](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download)将 KEK 公钥下载到 pem 文件中。 使用 KEK 公钥对导入的目标密钥进行加密。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

将 KEKforBYOK. publickey 文件传输到你的脱机工作站。 下一步将需要此文件。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>步骤3：生成并准备要传输的密钥

请参阅 HSM 供应商的文档，下载并安装 BYOK 工具。 按照 HSM 供应商提供的说明生成目标密钥，然后创建密钥传输包（BYOK 文件）。 BYOK 工具将使用你在[步骤 2](#step-2-download-kek-public-key)中下载的[步骤 1](#step-1-generate-a-kek)和 KEKforBYOK. publickey 文件中的密钥标识符在 BYOK 文件中生成加密目标密钥。

将 BYOK 文件传输到连接工作站。

> [!NOTE] 
> 目标密钥必须是2048位或3072位或4096位的 RSA 密钥。 此时不支持导入椭圆曲线密钥。
> <br/><strong>已知问题：</strong>从 SafeNet Luna Hsm 导入 RSA 4K 目标密钥失败。 解决问题后，将更新此文档。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>步骤4：将密钥传输到 Azure Key Vault

对于这最后一步，请将密钥传输包（BYOK 文件）从断开连接的工作站传输到连接 Internet 的工作站，然后使用[az keyvault Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令将 BYOK 文件上传 Azure Key Vault HSM，以完成密钥导入。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上传成功，将显示刚刚导入的密钥的属性。

## <a name="next-steps"></a>后续步骤

现在可以在密钥保管库中使用此受 HSM 保护的密钥。 有关详细信息，请参阅此价格和功能[比较](https://azure.microsoft.com/pricing/details/key-vault/)。
