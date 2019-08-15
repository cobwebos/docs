---
title: 使用 CLI 管理 Azure 密钥保管库 - Azure 密钥保管库 | Microsoft Docs
description: 使用本文通过 Azure CLI 自动执行密钥保管库中的常见任务
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 952126b76b00ff472181c72b1fd7b1a0e1b14e40
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976349"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>使用 Azure CLI 管理密钥保管库 

本文介绍如何开始使用 Azure CLI 处理 Azure 密钥保管库。  你可以获得以下信息：

- 如何在 Azure 中创建强化的容器（保管库）
- 将密钥、机密或证书添加到密钥保管库
- 将应用程序注册到 Azure Active Directory
- 授权应用程序使用密钥或机密
- 设置密钥保管库高级访问策略
- 使用硬件安全模块 (HSM)
- 删除密钥保管库以及关联的密钥和机密
- 其他 Azure 跨平台命令行接口命令


在大多数区域中提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

> [!NOTE]
> 本文未说明如何编写其中一个步骤所包括的 Azure 应用程序，但说明了如何授权应用程序使用 Key Vault 中的密钥或机密。
>

有关 Azure Key Vault 的概述，请参阅[什么是 Azure Key Vault？](key-vault-whatis.md)
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要使用本文中的 Azure CLI 命令，必须准备好以下各项：

* Microsoft Azure 订阅。 如果没有，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* Azure 命令行接口版本 2.0 或更高版本。 若要安装最新版本，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
* 配置为使用本文中所创建的密钥或密码的应用程序。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=45343)获取示例应用程序。 有关说明，请参阅随附的自述文件。

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>获得 Azure 跨平台命令行接口帮助

本文假设你熟悉命令行接口（Bash、终端、命令提示符）。

可以使用 --help 或 -h 参数来查看特定命令的帮助。 或者，也可以使用 Azure help [命令] [选项] 格式。 如果对某个命令所需的参数有疑问，请参阅帮助。 例如，以下命令都返回相同信息：

```azurecli
az account set --help
az account set -h
```

也可以阅读以下文章来熟悉如何在 Azure 跨平台命令行接口中使用 Azure 资源管理器：

* [安装 Azure CLI](/cli/azure/install-azure-cli)
* [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>如何在 Azure 中创建强化的容器（保管库）

保管库是由硬件安全模块支持的安全容器。 保管库可以集中存储应用程序机密，降低安全信息意外丢失的可能性。 Key Vault 还控制并记录外界对其所存储内容的访问。 Azure Key Vault 负责处理传输层安全性 (TLS) 证书的请求和续订事宜，其提供的功能是可靠的证书生命周期管理解决方案所必需的。 在后续步骤中，将创建一个保管库。

### <a name="connect-to-your-subscriptions"></a>连接到订阅

若要以交互方式登录，请使用以下命令：

```azurecli
az login
```
若要使用组织帐户登录，可以传入自己的用户名和密码。

```azurecli
az login -u username@domain.com -p password
```

如果有多个订阅，并需要指定要使用的订阅，请键入以下内容以查看帐户的订阅：

```azurecli
az account list
```

使用订阅参数指定订阅。

```azurecli
az account set --subscription <subscription name or ID>
```

有关配置 Azure 跨平台命令行接口的详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-a-new-resource-group"></a>创建新的资源组

使用 Azure 资源管理器时，会在资源组中创建所有相关资源。 可在现有的资源组中创建 Key Vault。 如果想要使用新资源组，可以新建一个。

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

第一个参数是资源组名称，第二个参数是位置。 若要获取所有可能的位置类型的列表：

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>注册密钥保管库资源提供程序

 尝试创建新的 Key Vault 时，可能会看到错误“订阅未注册，无法使用命名空间‘Microsoft.KeyVault’”。 如果显示此消息，请确保在订阅中注册 Key Vault 资源提供程序。 对每个订阅而言，这都是一次性操作。

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>创建 key vault

使用 `az keyvault create` 命令来创建密钥保管库。 此脚本包含三个必需参数：资源组名称、密钥保管库名称和地理位置。

若要在位于“东亚”的资源组 **ContosoResourceGroup** 中创建名为 **ContosoKeyVault** 的新保管库，请键入：  

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

此命令的输出会显示创建的 Key Vault 的属性。 两个最重要的属性是：

* **名称**：在本示例中，名称为 ContosoKeyVault。 将在其他 Key Vault 命令中使用此名称。
* **vaultUri**：在本示例中，URI 为 https://contosokeyvault.vault.azure.net 。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

Azure 帐户现已获取在此密钥保管库上执行任何作业的授权。 到目前为止，尚未授权其他任何人。

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>将密钥、机密或证书添加到密钥保管库

若要在 Azure Key Vault 中创建一个受软件保护的密钥，请使用 `az key create` 命令。

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

如果在 .pem 文件中保留了现有的密钥，可将此文件上传到 Azure Key Vault。 可以选择使用软件或 HSM 保护密钥。 此示例从 .pem 文件导入密钥，并使用密码“hVFkk965BuUv”通过软件对其进行保护：

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

现在可以通过使用密钥的 URI，引用已创建或上传到 Azure Key Vault 的密钥。 使用 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** 始终会获取当前版本。 使用 https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] 会获取此特定版本。 例如， **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 。 

将机密（名为 SQLPassword 的密码，其值为“hVFkk965BuUv”）添加到 Azure 密钥保管库。 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

使用此密码的 URI 引用此密码。 使用 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** 始终会获取当前版本，使用 https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] 会获取此特定版本。 例如， **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** 。

使用 .pem 或 .pfx 将证书导入保管库。

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

让我们查看创建的密钥、机密或证书：

* 若要查看密钥，请键入： 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* 若要查看机密，请键入： 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* 若要查看证书，请键入： 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>将应用程序注册到 Azure Active Directory

此步骤通常由开发人员在独立的计算机上完成。 此步骤并非特定于 Azure Key Vault，本文介绍此步骤仅供你了解。 若要完成应用注册，你的帐户、保管库和应用程序需在同一个 Azure 目录中。

使用密钥保管库的应用程序必须使用 Azure Active Directory 的令牌进行身份验证。  应用程序的所有者必须先将其注册到 Azure Active Directory 中。 注册结束后，应用程序所有者会获得以下值：

- **应用程序 ID**（也称为 AAD 客户端 ID 或 appID）
- **身份验证密钥**（也称共享机密）。 

应用程序必须向 Azure Active Directory 提供这两个值才能获取令牌。 如何将应用程序配置为获取令牌取决于应用程序。 对于 [Key Vault 示例应用程序](https://www.microsoft.com/download/details.aspx?id=45343)，应用程序所有者会在 app.config 文件中设置这些值。

有关向 Azure Active Directory 注册应用程序的详细步骤，请查看标题为[将应用程序与 Azure Active Directory 集成](../active-directory/develop/active-directory-integrating-applications.md)、[使用门户创建可访问资源的 Azure ActiveDirectory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)和[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)的文章。

在 Azure Active Directory 中注册应用程序：

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>授权应用程序使用密钥或机密

若要授权应用程序访问保管库中的密钥或机密，请使用 `az keyvault set-policy` 命令。

例如，如果保管库名称是 ContosoKeyVault，应用程序的 appID 为 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed，并且你想要授权应用程序使用保管库中的密钥来进行解密和签名，请使用以下命令：

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

若要授权同一应用程序读取保管库中的机密，请键入以下命令：

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> 设置密钥保管库高级访问策略

使用 [az keyvault update](/cli/azure/keyvault#az-keyvault-update) 为 Key Vault 启用高级策略。

 为部署启用密钥保管库：允许虚拟机从保管库中检索作为机密存储的证书。

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

为磁盘加密启用密钥保管库：使用保管库进行 Azure 磁盘加密时需要。

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

为模板部署启用密钥保管库：允许资源管理器从保管库中检索机密。

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>使用硬件安全模块 (HSM)

为了提高可靠性，可以在硬件安全模块 (HSM) 中导入或生成永不超出 HSM 边界的密钥。 这些 HSM 都通过 FIPS 140-2 第 2 级验证。 如果此要求对你不适用，请跳过本部分并转到删除密钥保管库以及关联的密钥和机密。

若要创建这些受 HSM 保护的密钥，必须具有支持 HSM 保护密钥的保管库订阅。

创建密钥保管库时，添加“sku”参数：

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

可以将软件保护的密钥（如前所示）和 HSM 保护的密钥添加到此保管库。 若要创建受 HSM 保护的密钥，请将 Destination 参数设为“HSM”：

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

可以使用以下命令，从计算机上的 .pem 文件导入密钥。 此命令会将密钥导入到密钥保管库服务中的 HSM：

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

下一个命令会导入“自带密钥”(BYOK) 包。 这样，便可以在本地 HSM 中生成密钥，并将其传输到密钥保管库服务中的 HSM，而密钥不会超出 HSM 界限：

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

有关如何生成此 BYOK 包的详细说明，请参阅[如何对 Azure 密钥保管库使用受 HSM 保护的密钥](key-vault-hsm-protected-keys.md)。

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>删除密钥保管库以及关联的密钥和机密

如果不再需要 Key Vault 及其密钥或机密，可以使用 `az keyvault delete` 命令删除 Key Vault：

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

或者，可以删除整个 Azure 资源组，其中包括密钥保管库和你加入该组的任何其他资源：

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>其他 Azure 跨平台命令行接口命令

可能有助于管理 Azure Key Vault 的其他命令。

此命令列出以表格形式显示的所有密钥和所选属性：

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

此命令显示特定密钥的完整属性列表：

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

此命令列出以表格形式显示的所有机密名称和所选属性：

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

下面是演示如何删除特定密钥的示例：

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

下面是演示如何删除特定机密的示例：

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>后续步骤

- 有关密钥保管库命令的完整 Azure CLI 参考，请参阅 [Key Vault CLI 参考](/cli/azure/keyvault)。

- 有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)

- 有关 Azure Key Vault 和 HSM 的信息，请参阅[如何将受 HSM 保护的密钥与 Azure Key Vault 结合使用](key-vault-hsm-protected-keys.md)。
