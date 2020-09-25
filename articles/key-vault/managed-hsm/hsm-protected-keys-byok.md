---
title: 如何为 Azure Key Vault 托管 HSM Azure Key Vault 生成和传输受 HSM 保护的密钥 |Microsoft Docs
description: 使用本文来帮助你规划、生成并传输自己的受 HSM 保护的密钥，以便与托管 HSM 一起使用。 也称为创建自己的密钥 (BYOK)。
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: ce275674704cd7538f25a2d3f31d0e65aecd7925
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320619"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>将受 HSM 保护的密钥导入到托管 HSM (BYOK) 

 Azure Key Vault 托管 HSM 支持导入内部硬件安全模块中生成的密钥 (HSM) ;密钥永远不会离开 HSM 保护边界。 此场景通常被称为创建自己的密钥 (BYOK)。 托管 HSM 使用 Marvell LiquidSecurity HSM 适配器 (FIPS 140-2 第3级验证的) 来保护密钥。

使用本文中的信息来帮助你规划、生成并传输自己的受 HSM 保护的密钥，以便与托管 HSM 一起使用。

> [!NOTE]
> 此功能不适用于 Azure 中国世纪互联。 此导入方法仅适用于[支持的 HSM](#supported-hsms)。 

有关详细信息以及有关如何开始使用托管 HSM 的教程，请参阅 [什么是托管 hsm？](overview.md)。

## <a name="overview"></a>概述

这里是编写过程的概述。 本文后面将介绍要完成的具体步骤。

* 在托管 HSM 中，生成一个密钥 (称为 *密钥交换密钥* (KEK) # A3。 KEK 必须是仅具有 `import` 密钥操作的 RSA-HSM 密钥。 
* 以 .pem 文件形式下载 KEK 公钥。
* 将 KEK 公钥传输到连接到本地 HSM 的脱机计算机。
* 在脱机计算机中，使用 HSM 供应商提供的 BYOK 工具来创建 BYOK 文件。 
* 使用 KEK 加密目标密钥，该密钥将保持加密状态，直到将其传输到托管的 HSM。 只有加密版本的密钥会离开本地 HSM。
* 托管 HSM 内生成的 KEK 不可导出。 Hsm 强制执行规则，而托管 HSM 外不存在 KEK 的明文版本。
* KEK 必须位于将导入目标密钥的托管 HSM 中。
* 将 BYOK 文件上传到托管 HSM 后，托管 HSM 将使用 KEK 私钥来解密目标密钥材料，并将其作为 HSM 密钥导入。 此操作完全在 HSM 内发生。 目标密钥始终保留在 HSM 保护边界内。


## <a name="prerequisites"></a>先决条件

若要使用本文中的 Azure CLI 命令，必须准备好以下各项：

* Microsoft Azure 订阅。 如果没有，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 版本2.12.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
* 托管 HSM 在你的订阅中 [支持的 hsm 列表](#supported-hsms) 。 请参阅 [快速入门：使用 Azure CLI 预配和激活托管 hsm](quick-create-cli.md) 以便预配和激活托管 hsm。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

若要详细了解通过 CLI 使用的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="supported-hsms"></a>支持的 HSM

|供应商名称|供应商类型|支持的 HSM 模型|详细信息|
|---|---|---|---|
|nCipher|制造商，<br/>HSM 即服务|<ul><li>HSM 的 nShield 系列</li><li>nShield 即服务</ul>|[nCipher 新的 BYOK 工具和文档](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|制造商|<ul><li>固件版本为 7.3 或更高版本的 Luna HSM 7 系列</li></ul>| [Luna BYOK 工具和文档](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|制造商，<br/>HSM 即服务|<ul><li>自防御密钥管理服务 (SDKMS)</li><li>Equinix SmartKey</li></ul>|[将 SDKMS 密钥导出到 BYOK 的云提供程序 - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|制造商|所有具有以下固件版本的 LiquidSecurity HSM<ul><li>固件版本 2.0.4 或更高版本</li><li>固件版本 3.2 或更高版本</li></ul>|[Marvell BYOK 工具和文档](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV（企业密钥管理系统）|多个 HSM 品牌和型号，包括<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>有关详细信息，请参阅 [Cryptomathic 站点](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK 工具和文档](https://www.cryptomathic.com/azurebyok)|

## <a name="supported-key-types"></a>支持的密钥类型

|项名|密钥类型|密钥大小|源|说明|
|---|---|---|---|---|
|密钥交换密钥 (KEK)|RSA| 2,048 位<br />3,072 位<br />4,096 位|托管的 HSM|托管 HSM 中生成的支持 HSM 的 RSA 密钥对|
|目标密钥|RSA|2,048 位<br />3,072 位<br />4,096 位|供应商 HSM|要传输到托管 HSM 的密钥|

## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>生成密钥并将其传输到托管 HSM

生成密钥并将其传输到托管 HSM：

* [步骤 1：生成 KEK](#step-1-generate-a-kek)
* [步骤 2：下载 KEK 公钥](#step-2-download-the-kek-public-key)
* [步骤 3：生成并准备要传输的密钥](#step-3-generate-and-prepare-your-key-for-transfer)
* [步骤4：将密钥传输到托管 HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>步骤 1：生成 KEK

KEK 是在托管 HSM 中生成的 RSA 密钥。 KEK 用于对要导入的密钥（目标密钥）进行加密。

KEK 必须满足以下条件：
- RSA-HSM 密钥（2,048 位；3,072 位；或 4,096 位）
- 在你打算导入目标密钥的相同托管 HSM 中生成
- 在允许的关键操作设置为 `import` 的情况下创建

> [!NOTE]
> KEK 必须将“import”作为唯一允许的密钥操作。 “import”与所有其他密钥操作是互斥的。

使用 [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-create) 命令创建将密钥操作设置为 `import` 的 KEK。 记录从以下命令返回的密钥标识符 (`kid`)。 （你将使用[步骤 3](#step-3-generate-and-prepare-your-key-for-transfer) 中的 `kid` 值。）

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>步骤 2：下载 KEK 公钥

使用 [az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-download) 将 KEK 公钥下载到 .pem 文件。 导入的目标密钥是使用 KEK 公钥进行加密的。

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

将 KEKforBYOK.publickey.pem 文件传输到脱机计算机。 下一步骤需要用到该文件。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>步骤 3：生成并准备要传输的密钥

请参阅 HSM 供应商的文档，下载并安装 BYOK 工具。 按照 HSM 供应商提供的说明生成目标密钥，然后创建密钥传输包（BYOK 文件）。 BYOK 工具将使用[步骤 1](#step-1-generate-a-kek) 中的 `kid` 和[步骤 2](#step-2-download-the-kek-public-key) 中下载的 KEKforBYOK.publickey.pem 文件在 BYOK 文件中生成加密的目标密钥。

将 BYOK 文件传输到连接的计算机。

> [!NOTE] 
> 不支持导入 RSA 1,024 位密钥。 当前不支持导入椭圆曲线 (EC) 密钥。
>
> 已知问题：仅固件版本 7.4.0 或更高版本支持从 Luna HSM 导入 RSA 4K 目标密钥。

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>步骤4：将密钥传输到托管 HSM

若要完成密钥导入，请将密钥传输包（BYOK 文件）从断开连接的计算机传输到连接到 Internet 的计算机。 使用 [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-import) 命令将 BYOK 文件上传到托管 HSM。

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上传成功，Azure CLI 将显示导入密钥的属性。

## <a name="next-steps"></a>后续步骤

现在，你可以在托管 HSM 中使用此 HSM 保护的密钥。 有关详细信息，请参阅[此价格和功能比较](https://azure.microsoft.com/pricing/details/key-vault/)。



