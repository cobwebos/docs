---
title: 如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥 - Azure Key Vault | Microsoft Docs
description: 使用本文来帮助你规划、生成并传输自己的受 HSM 保护的密钥，以便与 Azure Key Vault 一起使用。 也称为自带密钥（BYOK）。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 0e3246f9da202b54cc0d1285795c25cfafb678d8
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207024"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>将受 HSM 保护的密钥导入到 Key Vault（预览版）

> [!NOTE]
> 此功能处于预览阶段，仅适用于 Azure 区域 "*美国东部 2 EUAP* " 和 "*美国中部" EUAP*。 

若要在使用 Azure Key Vault 时添加保证，可以在硬件安全模块（HSM）中导入或生成密钥;密钥永远不会离开 HSM 边界。 这种情况通常称为 "*自带密钥*" （BYOK）。 Key Vault 使用 Hsm （FIPS 140-2 Level 2）的 nCipher nShield 系列来保护密钥。

使用本文中的信息来帮助你规划、生成并传输自己的受 HSM 保护的密钥，以便与 Azure Key Vault 一起使用。

> [!NOTE]
> 此功能不适用于 Azure 中国世纪互联。 
> 
> 此 import 方法仅适用于[受支持的 hsm](#supported-hsms)。 

有关详细信息，以及有关如何开始使用 Key Vault （包括如何为 HSM 保护的密钥创建密钥保管库）的教程，请参阅[什么是 Azure Key Vault？](key-vault-overview.md)。

## <a name="overview"></a>概述

下面是此过程的概述。 本文的后面部分将介绍完成的特定步骤。

* 在 Key Vault 中，生成一个密钥（称为*密钥交换密钥*（KEK））。 KEK 必须是仅具有 `import` 密钥操作的 RSA-HSM 密钥。 仅 Key Vault 高级 SKU 支持 RSA-HSM 密钥。
* 下载作为 pem 文件的 KEK 公钥。
* 将 KEK 公钥传输到连接到本地 HSM 的脱机计算机。
* 在脱机计算机中，使用 HSM 供应商提供的 BYOK 工具来创建 BYOK 文件。 
* 使用 KEK 加密目标密钥，该密钥将保持加密状态，直到将其传输到 Key Vault HSM。 只有已加密版本的密钥才能离开本地 HSM。
* Key Vault HSM 内生成的 KEK 不可导出。 Hsm 强制执行规则，该规则在 Key Vault HSM 之外不存在 KEK 的明文版本。
* KEK 必须位于将导入目标密钥的同一密钥保管库中。
* 将 BYOK 文件上传到 Key Vault 时，Key Vault HSM 使用 KEK 私钥来解密目标密钥材料，并将其作为 HSM 密钥导入。 此操作完全在 Key Vault HSM 内发生。 目标密钥始终保留在 HSM 保护边界内。

## <a name="prerequisites"></a>先决条件

下表列出了在 Azure Key Vault 中使用 BYOK 的先决条件：

| 要求 | 详细信息 |
| --- | --- |
| Azure 订阅 |若要在 Azure Key Vault 中创建密钥保管库，需要一个 Azure 订阅。 [注册免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 |
| 一个 Key Vault 高级 SKU，用于导入受 HSM 保护的密钥 |有关 Azure Key Vault 中的服务层和功能的详细信息，请参阅[Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)。 |
| 受支持的 Hsm 列表中的 HSM 和 HSM 供应商提供的 BYOK 工具和说明 | 你必须具有 HSM 的权限以及如何使用 HSM 的基本知识。 请参阅[支持的 hsm](#supported-hsms)。 |
| Azure CLI 版本2.1.0 或更高版本 | 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。|

## <a name="supported-hsms"></a>支持的 Hsm

|HSM 供应商名称|支持的 HSM 模型|详细信息|
|---|---|---|
|Thales|SafeNet Luna HSM 7 系列固件版本7.3 或更高版本| [SafeNet Luna BYOK 工具和文档](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|

> [!NOTE]
> 若要从 Hsm 的 nCipher nShield 系列导入受 HSM 保护的密钥，请使用[旧版 BYOK 过程](hsm-protected-keys-legacy.md)。

## <a name="supported-key-types"></a>支持的密钥类型

|项名|密钥类型|密钥大小|源|说明|
|---|---|---|---|---|
|密钥交换密钥（KEK）|RSA| 2048位<br />3072位<br />4096位|Azure Key Vault HSM|在 Azure Key Vault 中生成的 HSM 支持的 RSA 密钥对|
|目标密钥|RSA|2048位<br />3072位<br />4096位|供应商 HSM|要传输到 Azure Key Vault HSM 的密钥|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>生成密钥并将其传输到 Key Vault HSM

生成密钥并将其传输到 Key Vault HSM：

* [步骤1：生成 KEK](#step-1-generate-a-kek)
* [步骤2：下载 KEK 公钥](#step-2-download-the-kek-public-key)
* [步骤3：生成并准备要传输的密钥](#step-3-generate-and-prepare-your-key-for-transfer)
* [步骤4：将密钥传输到 Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>步骤1：生成 KEK

KEK 是在 Key Vault HSM 中生成的 RSA 密钥。 KEK 用于对要导入的密钥（*目标*密钥）进行加密。

KEK 必须是：
- RSA-HSM 密钥（2048位; 3072 位; 或4096位）
- 在你打算导入目标密钥的同一密钥保管库中生成
- 已使用允许的密钥操作设置为 `import`

> [!NOTE]
> KEK 必须具有 "import" 作为唯一允许的键操作。 "import" 与所有其他键操作是互相排斥的。

使用[az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令创建具有设置为 `import`的关键操作的 KEK。 记录从以下命令返回的密钥标识符（`kid`）。 （你将在[步骤 3](#step-3-generate-and-prepare-your-key-for-transfer)中使用 `kid` 值。）

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>步骤2：下载 KEK 公钥

使用[az keyvault key 下载](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download)将 KEK 公钥下载到 pem 文件。 导入的目标密钥是使用 KEK 公钥进行加密的。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

将 KEKforBYOK. publickey 文件传输到脱机计算机。 下一步将需要此文件。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>步骤3：生成并准备要传输的密钥

请参阅 HSM 供应商的文档，下载并安装 BYOK 工具。 按照 HSM 供应商提供的说明生成目标密钥，然后创建密钥传输包（BYOK 文件）。 BYOK 工具将使用[步骤 1](#step-1-generate-a-kek)和[步骤 2](#step-2-download-the-kek-public-key)中下载的 KEKforBYOK 文件中的 `kid`，在 BYOK 文件中生成加密目标密钥。

将 BYOK 文件传输到连接的计算机。

> [!NOTE] 
> 不支持导入 RSA 1024 位密钥。 当前不支持导入椭圆曲线（EC）键。
> 
> **已知问题**：仅固件 v7.4.0 或更高版本支持从 SafeNet Luna hsm 导入 RSA 4k 目标密钥。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>步骤4：将密钥传输到 Azure Key Vault

若要完成密钥导入，请将密钥传输包（BYOK 文件）从断开连接的计算机传输到连接到 internet 的计算机。 使用[az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令将 BYOK 文件上传到 Key Vault HSM。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上传成功，Azure CLI 将显示导入的密钥的属性。

## <a name="next-steps"></a>后续步骤

现在可以在密钥保管库中使用此受 HSM 保护的密钥。 有关详细信息，请参阅[此价格和功能比较](https://azure.microsoft.com/pricing/details/key-vault/)。



