---
title: 如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥 - Azure Key Vault | Microsoft Docs
description: 使用本文可帮助您规划、生成和传输自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。 也称为自带密钥 （BYOK）。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080129"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>将受 HSM 保护的密钥导入到 Key Vault（预览版）

> [!NOTE]
> 此功能处于预览状态，仅在 Azure 区域中可用，*东部美国 2 EUAP*和美国*中部 EUAP。* 

为了在使用 Azure 密钥保管库时添加保证，可以在硬件安全模块 （HSM） 中导入或生成密钥;密钥永远不会离开 HSM 边界。 此方案通常称为*自带密钥*（BYOK）。 密钥保管库使用 nCipher nShield 系列 HSM（FIPS 140-2 级验证）来保护密钥。

使用本文中的信息可帮助您规划、生成和传输自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。

> [!NOTE]
> 此功能不适用于 Azure 中国 21Vianet。 
> 
> 此导入方法仅适用于[受支持的 HSM。](#supported-hsms) 

有关详细信息，以及如何开始使用密钥保管库（包括如何为受 HSM 保护的密钥创建密钥保管库）的教程，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)

## <a name="overview"></a>概述

这里是编写过程的概述。 本文稍后将介绍要完成的具体步骤。

* 在密钥保管库中，生成密钥（称为*密钥交换密钥*（KEK）。 KEK 必须是仅具有密钥操作的`import`RSA-HSM 密钥。 只有密钥保管库高级 SKU 支持 RSA-HSM 密钥。
* 将 KEK 公钥下载为 .pem 文件。
* 将 KEK 公钥传输到连接到本地 HSM 的脱机计算机。
* 在脱机计算机中，使用 HSM 供应商提供的 BYOK 工具创建 BYOK 文件。 
* 目标密钥使用 KEK 加密，KEK 保持加密，直到将其传输到密钥保管库 HSM。 只有密钥的加密版本才会离开本地 HSM。
* 在密钥保管库 HSM 中生成的 KEK 不可导出。 HSM 强制实施规则，即密钥保管库 HSM 之外不存在 KEK 的明确版本。
* KEK 必须位于将导入目标密钥的同一密钥保管库中。
* 当 BYOK 文件上载到密钥保管库时，密钥保管库 HSM 使用 KEK 私钥解密目标密钥材料并将其导入为 HSM 密钥。 此操作完全发生在密钥保管库 HSM 中。 目标密钥始终保留在 HSM 保护边界中。

## <a name="prerequisites"></a>先决条件

下表列出了在 Azure 密钥保管库中使用 BYOK 的先决条件：

| 要求 | 详细信息 |
| --- | --- |
| Azure 订阅 |要在 Azure 密钥保管库中创建密钥保管库，需要 Azure 订阅。 [注册免费试用](https://azure.microsoft.com/pricing/free-trial/)。 |
| 用于导入 HSM 保护密钥的密钥保管库高级 SKU |有关 Azure 密钥保管库中的服务层和功能的详细信息，请参阅[密钥保管库定价](https://azure.microsoft.com/pricing/details/key-vault/)。 |
| 支持 HSM 列表的 HSM 和 BYOK 工具和 HSM 供应商提供的说明 | 您必须拥有 HSM 的权限，并具备如何使用 HSM 的基本知识。 请参阅[支持的 HSM](#supported-hsms)。 |
| Azure CLI 版本 2.1.0 或更高版本 | 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。|

## <a name="supported-hsms"></a>支持 HSM

|供应商名称|供应商类型|支持的 HSM 型号|详细信息|
|---|---|---|---|
|泰莱斯|制造商|SafeNet Luna HSM 7 系列，固件版本 7.3 或更高版本| [SafeNet Luna BYOK 工具和文档](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|福塔尼克斯|HSM 作为服务|自防密钥管理服务 （SDKMS）|[将 SDKMS 密钥导出到 BYOK 的云提供商 - Azure 密钥保管库](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> 要从 nCipher nShield 系列导入受 HSM 保护的密钥，请使用[传统的 BYOK 过程](hsm-protected-keys-legacy.md)。

## <a name="supported-key-types"></a>支持密钥类型

|项名|密钥类型|密钥大小|源|描述|
|---|---|---|---|---|
|密钥交换密钥 （KEK）|RSA| 2，048 位<br />3，072 位<br />4，096 位|Azure 密钥保管库 HSM|在 Azure 密钥保管库中生成的由 HSM 支持的 RSA 密钥对|
|目标密钥|RSA|2，048 位<br />3，072 位<br />4，096 位|供应商 HSM|要传输到 Azure 密钥保管库 HSM 的密钥|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>生成密钥并将其传输到密钥保管库 HSM

要生成密钥并将其传输到密钥保管库 HSM：

* [第 1 步：生成 KEK](#step-1-generate-a-kek)
* [第 2 步：下载 KEK 公钥](#step-2-download-the-kek-public-key)
* [第 3 步：生成并准备密钥进行传输](#step-3-generate-and-prepare-your-key-for-transfer)
* [步骤 4：将密钥传输到 Azure 密钥保管库](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>第 1 步：生成 KEK

KEK 是在密钥保管库 HSM 中生成的 RSA 密钥。 KEK 用于加密要导入的密钥（*目标*密钥）。

KEK 必须：
- RSA-HSM 键（2，048 位;3，072 位;或 4，096 位）
- 在要导入目标密钥的同一密钥保管库中生成
- 使用允许的键操作设置为`import`

> [!NOTE]
> KEK 必须具有"导入"作为唯一允许的密钥操作。 "导入"与所有其他关键操作是互斥的。

使用[az 密钥库密钥创建](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令创建将键操作设置为`import`的 KEK。 记录从以下命令返回`kid`的密钥标识符 （ ）。 （您将使用步骤`kid`[3](#step-3-generate-and-prepare-your-key-for-transfer)中的值 。）

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>第 2 步：下载 KEK 公钥

使用[az 密钥库密钥下载](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download)将 KEK 公钥下载到 .pem 文件。 使用 KEK 公钥加密您导入的目标密钥。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

将 KEKforBYOK.publickey.pem 文件传输到脱机计算机。 在下一步中，您将需要此文件。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>第 3 步：生成并准备密钥进行传输

请参阅 HSM 供应商的文档以下载并安装 BYOK 工具。 按照 HSM 供应商的说明生成目标密钥，然后创建密钥传输包（BYOK 文件）。 BYOK 工具将使用`kid`从步骤[1](#step-1-generate-a-kek)和 KEKforBYOK.publickey.pem 文件在步骤[2](#step-2-download-the-kek-public-key)中下载，以在 BYOK 文件中生成加密的目标密钥。

将 BYOK 文件传输到已连接的计算机。

> [!NOTE] 
> 不支持导入 RSA 1，024 位密钥。 目前，不支持导入椭圆曲线 （EC） 键。
> 
> **已知问题**：仅支持从 SafeNet Luna HSM 导入 RSA 4K 目标密钥，但固件 7.4.0 或更高版本才受支持。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>步骤 4：将密钥传输到 Azure 密钥保管库

要完成密钥导入，请将密钥传输包（BYOK 文件）从断开连接的计算机传输到联网计算机。 使用[az 密钥库密钥导入](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令将 BYOK 文件上载到密钥保管库 HSM。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上载成功，Azure CLI 将显示导入密钥的属性。

## <a name="next-steps"></a>后续步骤

现在可以在密钥保管库中使用此受 HSM 保护的密钥。 有关详细信息，请参阅[此价格和功能比较](https://azure.microsoft.com/pricing/details/key-vault/)。



