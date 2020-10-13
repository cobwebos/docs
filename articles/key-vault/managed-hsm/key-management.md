---
title: 管理托管 HSM 中的密钥 - Azure Key Vault |Microsoft Docs
description: 学习本文内容，了解如何管理托管 HSM 中的密钥
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 846153dd482130bbb3b35c38a3dbb791e0d0d32e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448273"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>使用 Azure CLI 管理托管 HSM

> [!NOTE]
> Key Vault 支持两种类型的资源：保管库和托管 HSM。 本文介绍托管 HSM。 若要了解如何管理保管库，请参阅[使用 Azure CLI 管理密钥保管库](../general/manage-with-cli2.md)。

有关托管 HSM 的概述，请参阅[什么是托管 HSM？](overview.md)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，必须准备好以下项：

* Microsoft Azure 订阅。 如果没有，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 版本 2.12.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
* 订阅中的托管 HSM。 请参阅[快速入门：使用 Azure CLI 预配和激活托管 HSM](quick-create-cli.md)，预配和激活托管 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

若要详细了解通过 CLI 使用的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

> [!NOTE]
> 下面所有命令都显示了两种使用方法。 一种使用 --hsm-name 和 --name（对于密钥名称）参数，而另一种使用 --id 参数，你可在其中指定整个 URL，包括密钥名称（如果适用）  。 如果调用方（用户或应用程序）没有控制平面的读取访问权限，而且只有数据平面的受限访问权限，则后一种方法非常有用。

## <a name="create-an-hsm-key"></a>创建 HSM 密钥

使用 `az keyvault key create` 命令来创建密钥。

### <a name="create-an-rsa-key"></a>创建 RSA 密钥

下面的示例演示如何创建一个 3072 位 RSA 密钥，该密钥将仅用于 wrapKey、unwrapKey 操作 (--ops) 。 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

请注意，`get` 操作仅返回公钥和密钥属性。 它不会返回私钥（对于非对称密钥）或密钥材料（对于对称密钥）。

### <a name="create-an-ec-key"></a>创建 EC 密钥

下面的示例演示如何创建包含 P-256 曲线的 EC 密钥，该密钥将仅用于 sign 和 verify 操作 (--ops)，并有两个标记 usage 和 appname   。 标记有助于你将其他元数据添加到密钥，以便进行跟踪和管理。

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>创建 256 位对称密钥

下面的示例演示如何创建一个 256 位对称密钥，该密钥将仅用于 encrypt 和 decrypt 操作 (--ops) 。

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

## <a name="view-key-attributes-and-tags"></a>查看密钥属性和标记

使用 `az keyvault key show` 命令查看密钥的属性、版本和标记。

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>列出密钥

使用 `az keyvault key list` 命令列出托管 HSM 内的所有密钥。

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>删除密钥

使用 `az keyvault key delete` 命令删除托管 HSM 内的密钥。 请注意，软删除始终可用。 因此，删除的密钥将保留为已删除状态并且可恢复，但如果超过了保留天数，密钥将被清除（永久删除）而且无法恢复。

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>列出已删除的密钥

使用 `az keyvault key list-deleted` 命令列出托管 HSM 中处于已删除状态的所有密钥。

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>恢复（撤销删除）已删除的密钥

使用 `az keyvault key list-deleted` 命令列出托管 HSM 中处于已删除状态的所有密钥。 如果在恢复已删除的密钥时需要使用--id 参数恢复（撤销删除）某个密钥，必须记下从 `az keyvault key list-deleted` 命令获取的已删除密钥的 `recoveryId` 值。

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>清除（永久删除）密钥

使用 `az keyvault key purge` 命令清除（永久删除）密钥。

> [!NOTE]
> 如果托管 HSM 启用了清除保护，则不允许执行清除操作。 保留期结束后，密钥将自动清除。

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>创建单个密钥备份

使用 `az keyvault key backup` 创建密钥备份。 备份文件是一个加密的 blob，以加密方式绑定到源 HSM 的安全域。 它只能在共享同一安全域的 HSM 中还原。 详细了解[安全域](security-domain.md)。

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>从备份中还原单个密钥

使用 `az keyvault key restore` 还原单个密钥。 创建备份的源 HSM 必须与要在其中还原密钥的目标 HSM 共享同一安全域。

> [!NOTE]
> 如果存在处于活动或已删除状态的同名密钥，还原将不会成功。

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>从文件中导入密钥

使用 `az keyvault key import` 命令从文件中导入密钥（仅限 RSA 和 EC）。 证书文件必须具有私钥，并且必须使用 PEM 编码（如 RFC [1421](https://tools.ietf.org/html/rfc1421)、[1422](https://tools.ietf.org/html/rfc1422)、[1423](https://tools.ietf.org/html/rfc1423)、[1424](https://tools.ietf.org/html/rfc1424) 所定义）。

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

若要将密钥从本地 HSM 导入到托管 HSM，请参阅[将受 HSM 保护的密钥导入到托管 HSM (BYOK)](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>后续步骤

- 有关密钥保管库命令的完整 Azure CLI 参考，请参阅 [Key Vault CLI 参考](/cli/azure/keyvault)。
- 有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](../general/developers-guide.md)
- 详细了解[托管 HSM 角色管理](role-management.md)
- 详细了解[托管 HSM 最佳做法](best-practices.md)
