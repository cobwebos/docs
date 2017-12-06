---
title: "使用 CLI 管理 Azure Key Vault | Microsoft Docs"
description: "使用本教程通过 CLI 自动执行密钥保管库中的常见任务"
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>使用 CLI 管理密钥保管库

本教程会帮助你开始使用 Azure Key Vault 在 Azure 中创建强化容器（保管库）。 Azure Key Vault 用于存储和管理加密密钥和机密。 本文将指导你完成使用 Azure 跨平台命令行接口创建保管库的过程。 然后将与保管库进行交互以执行各种常见操作。 

在大多数区域中提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

**估计完成时间：** 20 分钟。

> [!NOTE]
> 本教程不包括有关如何编写其中一个步骤包括的 Azure 应用程序的说明。 此示例应用程序用于演示如何授权应用程序使用密钥保管库中存储的密钥或机密。
> 本文重点介绍如何使用跨平台命令行接口配置 Azure Key Vault。 有关 Azure PowerShell 说明，请参阅[此对应教程](key-vault-get-started.md)。

有关 Azure 密钥保管库的概述信息，请参阅 [什么是 Azure 密钥保管库？](key-vault-whatis.md)

## <a name="prerequisites"></a>先决条件

要完成本教程，必须准备好以下各项：

* Microsoft Azure 订阅。 如果没有订阅，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* 命令行接口版本 0.9.1 或更高版本。 若要安装最新版本并连接到 Azure 订阅，请参阅[安装和配置 Azure 跨平台命令行接口](../cli-install-nodejs.md)。
* 配置为使用在本教程中所创建的密钥或密码的应用程序。 可以从 [Microsoft 下载中心](http://www.microsoft.com/download/details.aspx?id=45343)获取示例应用程序。 有关说明，请参阅随附的自述文件。

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>获得 Azure 跨平台命令行接口帮助

本教程假定你熟悉命令行接口（Bash、终端、命令提示符）

可以使用 --help 或 -h 参数来查看特定命令的帮助。 或者，也可以使用 azure help [命令] [选项] 格式返回相同信息。 例如，以下命令都返回相同信息：

    azure account set --help

    azure account set -h

    azure help account set

当你对某一命令所需的参数有疑问时，请使用 --help、-h 或 azure help [命令] 来查看帮助。

还可阅读以下教程以熟悉如何在 Azure 跨平台命令行接口中使用 Azure 资源管理器部署模型：

* [如何安装和配置 Azure 跨平台命令行接口](../cli-install-nodejs.md)
* [将 Azure 跨平台命令行接口用于 Azure 资源管理器](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>连接到订阅

使用以下命令登录到 Azure：

```azurecli-interactive
azure login -u username -p password
```

或者如果想要通过以交互方式键入来登录

```azurecli-interactive
azure login
```

如果有多个订阅，并想要指定其中一个订阅供 Azure 密钥保管库使用，请键入以下内容以查看帐户的订阅：

```azurecli-interactive
azure account list
```

然后，要指定要使用的订阅，请键入：

```azurecli-interactive
azure account set <subscription name>
```

有关配置 Azure 跨平台命令行接口的详细信息，请参阅[如何安装和配置 Azure 跨平台命令行接口](../cli-install-nodejs.md)。

## <a name="switch-to-using-azure-resource-manager"></a>切换到使用 Azure 资源管理器
密钥保管库需要 Azure 资源管理器，因此请键入以下内容以切换到 Azure 资源管理器模式：

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>创建新的资源组
使用 Azure 资源管理器时，会在资源组中创建所有相关资源。 在本教程中，我们将创建新资源组“ContosoResourceGroup”。

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

第一个参数是资源组名称，第二个参数是位置。 对于位置，请使用命令 `azure location list` 来了解如何针对本示例中的位置指定替代位置。 如需更多信息，请键入：`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>注册密钥保管库资源提供程序
确保在订阅中注册密钥保管库资源提供程序：

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

此操作每个订阅仅需执行一次。

## <a name="create-a-key-vault"></a>创建密钥保管库

使用 `azure keyvault create` 命令来创建密钥保管库。 此脚本包含三个必需参数：资源组名称、密钥保管库名称和地理位置。

例如：
- 如果使用保管库名称 **ContosoKeyVault**、
- 资源组名称 **ContosoResourceGroup**、
- 位置“亚洲东部”，请键入：

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

此命令的输出会显示刚刚创建的密钥保管库的属性。 两个最重要的属性是：

* **名称**：在本示例中为 ContosoKeyVault。 将在其他密钥保管库 cmdlet 中使用此名称。
* **vaultUri**：在本示例中为 https://contosokeyvault.vault.azure.net。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

Azure 帐户现已获取在此密钥保管库上执行任何作业的授权。 而且没有其他人有此授权。

## <a name="add-a-key-or-secret-to-the-key-vault"></a>将密钥或密码添加到密钥保管库

如果希望 Azure 密钥保管库创建一个受软件保护的密钥，请使用 `azure key create` 命令，并键入以下内容：

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

但是，如果在保存为本地文件的 .pem 文件（名为 softkey.pem）中有现有密钥要上传到 Azure 密钥保管库，请键入以下命令以从 .PEM 文件（通过密钥保管库服务中的软件保护密钥）中导入该密钥：

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

现在可以通过使用密钥的 URI，引用已创建或上传到 Azure Key Vault 的密钥。 使用 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** 可始终获取最新版本，使用 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 可获取此特定版本。

要将名为 SQLPassword 且其 Azure 密钥保管库的值为 Pa$$w0rd 的机密添加到保管库，请键入以下内容：

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

现在，可以通过使用密码的 URI，引用已添加到 Azure 密钥保管库的此密码。 使用 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** 可获取最新版本，使用 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** 可获取此特定版本。

让我们查看一下刚刚创建的密钥或机密：

* 若要查看密钥，请键入：`azure keyvault key list --vault-name 'ContosoKeyVault'`
* 若要查看机密，请键入：`azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>将应用程序注册到 Azure Active Directory

此步骤通常由开发人员在独立的计算机上完成。 这并非 Azure 密钥保管库的特有状况，在此列出是为了让过程完整。

> [!IMPORTANT]
> 要完成本教程，帐户、保管库以及会在本步骤中注册的应用程序必须全都位于同一 Azure Active Directory 中。

使用密钥保管库的应用程序必须使用 Azure Active Directory 的令牌进行身份验证。 为此，应用程序的所有者首先必须在其 Azure Active Directory 中注册该应用程序。 注册结束后，应用程序所有者会获得以下值：

- **应用程序 ID** 
- **身份验证密钥**（也称共享机密）。 

应用程序必须向 Azure Active Directory 提供这两个值才能获取令牌。 如何将应用程序配置为执行此操作取决于应用程序。 对于 [Key Vault 示例应用程序](https://www.microsoft.com/download/details.aspx?id=45343)，应用程序所有者会在 app.config 文件中设置这些值。

如需将应用程序注册到 Azure Active Directory 的详细步骤，应参阅[将应用程序与 Azure Active Directory 集成](../active-directory/develop/active-directory-integrating-applications.md)一文或[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)一文。若要在 Azure Active Directory 中注册应用程序，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧单击“应用注册”。 如果没有看到应用注册，单击“更多服务”即可找到。  
    >[!NOTE]
    必须选择包含用于创建 Key Vault 的 Azure 订阅的相同目录。 
3. 单击“新建应用程序注册”。
4. 在“创建”边栏选项卡上提供应用程序的名称，然后选择“WEB 应用程序和/或 WEB API”（默认值）并指定 Web 应用程序的“登录 URL”。 对于此步骤，如果目前没有该信息，可以进行编造（例如，可以指定 http://test1.contoso.com）。 这些网站是否存在并不重要。 

   ![新建应用程序注册](./media/key-vault-manage-with-cli/new-application-registration.png)

5. 单击“创建”  按钮。
6. 完成应用注册以后，即可看到已注册应用的列表。 找到刚注册的应用，然后单击它。
7. 单击“已注册应用”边栏选项卡，然后复制**应用程序 ID**
8. 单击“所有设置”
9. 在“设置”边栏选项卡上，单击“密钥”
10. 在“密钥说明”框中键入说明，选择持续时间，然后单击“保存”。 页面会刷新，随后显示密钥值。 
11. 在下一步，需使用“应用程序 ID”和“密钥”信息来设置保管库的权限。

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>授权应用程序使用密钥或密码
若要授权应用程序访问保管库中的密钥或机密，请使用：

```azurecli-interactive
azure keyvault set-policy
```

例如，如果保管库名称是 ContosoKeyVault，要授权的应用程序的客户端 ID 为 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed，而希望授权应用程序使用保管库中的密钥来进行解密和签名，那么，请执行以下操作：

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> 如果是在 Windows 命令提示符下运行，则应将单引号替换为双引号，并对内部双引号进行转义操作。 例如：“[\"decrypt\",\"sign\"]”。
> 

如果要授权同一应用程序读取保管库中的机密，请运行以下命令：

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>如果要使用硬件安全模块 (HSM)
为了提高可靠性，可以在硬件安全模块 (HSM) 中导入或生成永不超出 HSM 边界的密钥。 这些 HSM 都通过 FIPS 140-2 第 2 级验证。 如果此要求对你不适用，请跳过本部分并转到[删除密钥保管库以及关联的密钥和机密](#delete-the-key-vault-and-associated-keys-and-secrets)。

若要创建这些受 HSM 保护的密钥，必须具有支持 HSM 保护密钥的保管库订阅。

创建密钥保管库时，添加“sku”参数：

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

可以将软件保护的密钥（如前所示）和 HSM 保护的密钥添加到此保管库。 若要创建受 HSM 保护的密钥，请将 Destination 参数设为“HSM”：

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

可以使用以下命令，从计算机上的 .pem 文件导入密钥。 此命令会将密钥导入到密钥保管库服务中的 HSM：

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

下一个命令会导入“自带密钥”(BYOK) 包。 这样，便可以在本地 HSM 中生成密钥，并将其传输到密钥保管库服务中的 HSM，而密钥不会超出 HSM 界限：

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

有关如何生成此 BYOK 包的详细说明，请参阅[如何对 Azure 密钥保管库使用受 HSM 保护的密钥](key-vault-hsm-protected-keys.md)。

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>删除密钥保管库以及关联的密钥和机密
如果不再需要密钥保管库及其包含的密钥或机密，可以使用 azure keyvault delete 命令来删除密钥保管库：

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

或者，可以删除整个 Azure 资源组，其中包括密钥保管库和你加入该组的任何其他资源：

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>其他 Azure 跨平台命令行接口命令
可能有助于管理 Azure 密钥保管库的其他命令。

此命令列出以表格形式显示的所有密钥和所选属性：

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

此命令显示特定密钥的完整属性列表：

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

此命令列出以表格形式显示的所有机密名称和所选属性：

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

下面是演示如何删除特定密钥的示例：

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

下面是演示如何删除特定机密的示例：

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>后续步骤
- 有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)。
- 有关 Azure 密钥保管库的概述信息，请参阅 [什么是 Azure 密钥保管库？](key-vault-whatis.md)
- 如何使用 PowerShell 处理 Azure Key Vault [Azure Key Vault 入门](key-vault-get-started.md)。


