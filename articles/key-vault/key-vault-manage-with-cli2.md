---
title: "管理 Azure 密钥保管库使用 CLI |Microsoft 文档"
description: "使用本教程通过使用 CLI 2.0 自动化密钥保管库中的常见任务"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ambapat
ms.openlocfilehash: 5da9f5eceda71ac85259193e0f183c72813e1679
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="manage-key-vault-using-cli-20"></a>管理密钥保管库使用 CLI 2.0
在大多数区域提供了 azure 密钥保管库。 有关详细信息，请参阅[密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="introduction"></a>简介
使用本教程来帮助你开始使用 Azure 密钥保管库在 Azure 中，来存储和管理加密密钥和机密在 Azure 中的创建强化的容器 （保管库）。 它将指导你完成使用 Azure 跨平台命令行界面创建包含密钥或密码，然后，可以使用与 Azure 应用程序的保管库的过程。 随后，还介绍如何应用程序可以然后使用该密钥或密码。

**估计完成时间：** 20 分钟

> [!NOTE]
> 本教程不包括有关如何编写 Azure 包括应用程序的步骤之一，其中说明了如何授权应用程序使用密钥或机密密钥保管库中的说明。
>
> 本教程使用最新的 Azure CLI 2.0。
>
>

有关 Azure 密钥保管库的概述信息，请参阅[什么是 Azure 密钥保管库？](key-vault-whatis.md)

## <a name="prerequisites"></a>必备条件
若要完成本教程，你必须：

* 订阅 Microsoft Azure。 如果你还没有，你可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* 命令行界面版本 2.0 或更高版本。 若要安装最新版本并连接到你的 Azure 订阅，请参阅[安装和配置 Azure 跨平台命令行界面 2.0](/cli/azure/install-azure-cli)。
* 将配置为使用的密钥或密码在本教程中创建一个应用程序。 示例应用程序可从[Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343)。 有关说明，请参阅随附的自述文件。

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>获取帮助使用 Azure 跨平台命令行接口
本教程假定你熟悉命令行界面 （Bash、 终端、 命令提示符）

--Help 或-h 参数可以用于查看特定命令的帮助。 或者，azure help [命令] 还可以使用 [选项] 格式返回相同信息。 例如，以下命令都返回相同信息：

```
az account set --help
az account set -h
```

在某一命令所需的参数有疑问，请参阅帮助使用--help、-h 或 az help [命令]。

此外可阅读以下教程以熟悉如何使用 Azure 资源管理器在 Azure 跨平台命令行界面中：

* [安装 Azure CLI](/cli/azure/install-azure-cli)
* [要开始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>连接到你的订阅
若要使用组织帐户登录，请使用以下命令：

```
az login -u username@domain.com -p password
```

或如果你想要通过以交互方式键入登录

```
az login
```

如果你有多个订阅，并想要指定其中一个要用于 Azure 密钥保管库，请键入以下内容来查看你的帐户的订阅：

```
az account list
```

然后，若要指定要使用的订阅，请键入：

```
az account set --subscription <subscription name or ID>
```

有关配置 Azure 跨平台命令行界面的详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-new-resource-group"></a>创建新的资源组
在使用 Azure 资源管理器时，在资源组中创建所有相关的资源。 出于本教程，我们将创建新的资源组 ContosoResourceGroup。

```
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

第一个参数是资源组名称，第二个参数是位置。 对于位置，使用命令`az account list-locations`来确定如何指定在此示例中的一个替代位置。 如果你需要详细信息，请键入： `az account list-locations -h`。

## <a name="register-the-key-vault-resource-provider"></a>注册密钥保管库资源提供程序
请确保你的订阅中注册了该密钥保管库资源提供程序：

```
az provider register -n Microsoft.KeyVault
```

此操作仅需执行一次每个订阅。

## <a name="create-a-key-vault"></a>创建密钥保管库
使用`az keyvault create`命令以创建密钥保管库。 此脚本包含三个必需参数： 一个资源组名称、 密钥保管库名称和地理位置。

例如，如果你使用保管库名称为 ContosoKeyVault，资源组名称为 ContosoResourceGroup 和亚洲东部的位置，键入：
```
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

此命令的输出显示你刚刚创建的密钥保管库的属性。 两个最重要属性包括：

* **名称**： 在此示例中为 ContosoKeyVault。 你将使用此名称的其他密钥保管库命令。
* **vaultUri**： 在示例中这是 https://contosokeyvault.vault.azure.net。 使用你通过其 REST API 的保管库的应用程序必须使用此 URI。

现在，你的 Azure 帐户有权执行此密钥保管库的任何操作。 尚未，没有其他人。

## <a name="add-a-key-or-secret-to-the-key-vault"></a>将密钥或机密添加到密钥保管库
如果你希望 Azure 密钥保管库为你创建一个受软件保护密钥，使用`az key create`命令时，并键入以下命令：
```
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
但是，如果你有现有密钥.pem 文件另存为本地文件中名为 softkey.pem 你想要将上载到 Azure 密钥保管库的文件中，键入以下内容以导入中的键。PEM 文件，以便软件在密钥保管库服务中保护密钥：
```
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```
现在，你可以引用创建或上载到 Azure 密钥保管库，通过使用其 URI 的密钥。 使用**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**可始终获取最新版本，而使用**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**可获取此特定版本。

要将的机密添加到保管库，其名为 SQLPassword 且值的 Pa$ $w0rd Azure 密钥保管库，键入以下命令：
```
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```
现在，你可以引用你通过使用其 URI 添加到 Azure 密钥保管库，此密码。 使用**https://ContosoVault.vault.azure.net/secrets/SQLPassword**可始终获取最新版本，而使用**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**可获取此特定版本。

让我们查看的密钥或你刚刚创建的机密：

* 若要查看你的密钥，请键入：`az keyvault key list --vault-name 'ContosoKeyVault'`
* 若要查看你的密码，请键入：`az keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory 中注册应用程序
此步骤通常会通过开发人员，在单独的计算机上。 它并不特定于 Azure 密钥保管库，但此列出是为了出于完整性的考虑。

> [!IMPORTANT]
> 若要完成本教程后，你的帐户、 保管库，并将在此步骤中注册应用程序都必须位于同一 Azure 目录中。
>
>

使用密钥保管库的应用程序必须通过使用来自 Azure Active Directory 的令牌进行身份验证。 为此，应用程序的所有者必须先注册应用程序在其 Azure Active Directory 中。 在注册结束时，应用程序所有者会收到以下值：

* **应用程序 ID** (也称为客户端 ID) 和**身份验证密钥**（也称为共享的机密）。 应用程序必须向 Azure Active Directory，以获取令牌提供这些值。 配置应用程序以执行此操作的方式取决于应用程序。 密钥保管库的示例应用程序，应用程序所有者的 app.config 文件中设置这些值。

在 Azure Active Directory 中注册该应用程序：

1. 登录到 Azure 门户。
2. 在左侧，单击**Azure Active Directory**，然后选择要在其中注册你的应用程序目录。 <br> <br> 

> [!Note] 
> 你必须选择包含用于创建密钥保管库的 Azure 订阅的相同目录。 如果你不知道哪个目录这变，请单击**设置**，找到用于创建密钥保管库，订阅，并记下最后一列中显示的目录的名称。

3. 单击**应用程序**。 如果你的目录中不添加任何应用程序，只会显示此页**添加应用程序**链接。 单击该链接，或也可以单击**添加**命令栏上。
4. 在**添加应用程序**向导**你希望做什么？**页上，单击**添加我的组织正在开发的应用程序**。
5. 上**告诉我们你的应用程序**页上，指定你的应用程序的名称，然后选择**WEB 应用程序和/或 WEB API** （默认值）。 单击下一步图标。
6. 上**应用程序属性**页上，指定**登入 URL**和**应用程序 ID URI** web 应用程序。 如果你的应用程序没有这些值，你可以使它们为此步骤 （例如，你可以指定两个框 http://test1.contoso.com）。 这些网站是否存在; 并不重要重要的是适用于每个应用程序的应用 ID URI 是在你的目录中的每个应用程序不同。 该目录使用此字符串来标识你的应用程序。
7. 单击完成图标以保存向导中所做的更改。
8. 在快速启动页上，单击**配置**。
9. 滚动到**密钥**部分，选择持续时间，，然后单击**保存**。 页面刷新，随后显示密钥值。 你必须使用此密钥值来配置你的应用程序和**客户端 ID**值。 （对于此配置的说明将是特定于应用程序。）
10. 复制从此页中，将在下一步中使用以在你的保管库上设置权限的客户端 ID 值。

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>授权应用程序使用密钥或机密
若要授权应用程序访问的密钥或机密保管库中的，使用`az keyvault set-policy`命令。

例如，如果保管库名称是 ContosoKeyVault 和你想要授权应用的程序有 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed，客户端 ID 和你想要授权要解密和使用你的保管库中的密钥签名的应用程序，然后运行以下命令：
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

如果你想要授权同一应用程序读取保管库中的机密，运行以下命令：
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>如果你想要使用硬件安全模块 (HSM)
为了提高可靠性，你可以导入或生成永不超出 HSM 边界的硬件安全模块 (Hsm) 中的密钥。 Hsm 是的 FIPS 140-2 Level 2 验证。 如果此要求不适用于你，跳过此部分并转到[删除密钥保管库以及关联的密钥和机密](#delete-the-key-vault-and-associated-keys-and-secrets)。

若要创建这些受 HSM 保护的密钥，你必须具有支持受 HSM 保护的密钥的保管库订阅。

当你创建 keyvault 时，添加 sku 参数：

```
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
你可以将软件保护密钥 （如前所示） 和 HSM 保护密钥添加到此保管库。 若要创建受 HSM 保护密钥，设置为 HSM Destination 参数：

```
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

可以使用以下命令以将密钥导入从你的计算机上的.pem 文件。 此命令将导入密钥到 Hsm 密钥保管库服务中：

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```
下一步的命令将导入"携带你自己的密钥"(BYOK) 包。 这样就可以在你本地 HSM 中生成你的密钥并将其传送到 Hsm 在密钥保管库服务中，而无需离开 HSM 边界的密钥：

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
有关详细说明有关如何生成此 BYOK 包，请参阅[如何将受 hsm 保护密钥与 Azure 密钥保管库使用](key-vault-hsm-protected-keys.md)。

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>删除密钥保管库以及关联的密钥和机密
如果你不再需要密钥保管库的密钥或机密它所包含，则可以通过删除密钥保管库`az keyvault delete`命令：

```
az keyvault delete --name 'ContosoKeyVault'
```

或者，你可以删除整个 Azure 资源组，其中包括密钥保管库和该组中包含的任何其他资源：

```
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>其他 Azure 跨平台命令行接口命令
其他命令，你可能会有助于管理 Azure 密钥保管库。

此命令列出以表格形式显示的所有密钥和所选的属性：

az keyvault 密钥列表--保管库名称 ContosoKeyVault

此命令显示指定键的属性的完整列表：

显示的 az keyvault 密钥-保管库名称 ContosoKeyVault-名称 ContosoFirstKey

此命令列出以表格形式显示的所有机密名称和所选的属性：

az keyvault 机密列表--保管库名称 ContosoKeyVault

下面是如何删除特定密钥的示例：

已删除 az keyvault 密钥-保管库名称 ContosoKeyVault-名称 ContosoFirstKey

下面是如何删除特定机密的示例：

已删除 az keyvault 密钥-保管库名称 ContosoKeyVault-名称 SQLPassword


## <a name="next-steps"></a>后续步骤
有关密钥保管库命令的完整 Azure CLI 参考，请参阅[密钥保管库 CLI 参考](/cli/azure/keyvault)

有关编程参考，请参阅[Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)。
