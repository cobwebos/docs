---
title: 有关具有两组凭据的资源的轮换教程
description: 通过本教程了解如何自动轮换使用两组身份验证凭据的资源的机密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: bf4864e0c6342cbd4729d5b99479eb2ef1a2c48c
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378213"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>自动轮换使用两组身份验证凭据的资源的机密

向 Azure 服务进行身份验证的最佳方法是使用[托管标识](../general/authentication.md)，但某些情况下无法做到这一点。 在此类情况下，将使用访问密钥或密码。 访问密钥和密码应经常轮换。

本教程介绍如何定期自动轮换使用两组身份验证凭据的数据库和服务的机密。 具体而言，本教程将使用 Azure 事件网格通知触发的函数来将 Azure Key Vault 中存储的 Azure 存储帐户密钥作为机密轮换。 解码的字符：

> [!NOTE]
> 通过为“针对存储帐户的委托访问”提供共享访问签名令牌，可以自动管理 Key Vault 中的存储帐户密钥。 有些服务需要带有访问密钥的存储帐户连接字符串，对于这种情况，建议使用此解决方案

![轮换解决方案示意图](../media/secrets/rotation-dual/rotation-diagram.png)

在上面的解决方案中，Azure Key Vault 将存储帐户的单个访问密钥存储为同一机密的不同版本，在后续版本中作为主密钥和辅助密钥交替使用。 当一个访问密钥存储到最新版本的机密中时，将重新生成备用密钥并作为新的最新版本的机密添加到密钥库中。 该解决方案为应用程序提供了完整的轮换周期，以便刷新到再生成的最新密钥。 

1. 在机密过期之前的 30 天，Key Vault 会向事件网格发布“即将过期”事件。
1. 事件网格会检查事件订阅，并使用 HTTP POST 调用已订阅该事件的函数应用终结点。
1. 函数应用标识备用密钥（而不是最新密钥）并调用存储帐户以再生成该密钥
1. 函数应用将新的再生成的密钥添加到 Azure Key Vault 中，作为密钥的新版本。

## <a name="prerequisites"></a>先决条件
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Key Vault
* 两个 Azure 存储帐户

如果没有现成的密钥保管库和存储帐户，可以使用下面的部署链接：

[![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. 在“资源组”下，选择“新建”。 将组命名为“akvrotation”，然后单击“确定” 。
1. 选择“查看 + 创建”。
1. 选择“创建”

    ![创建资源组](../media/secrets/rotation-dual/dual-rotation-1.png)

现在，你拥有一个密钥保管库和两个存储帐户。 可以在 Azure CLI 中运行以下命令来验证此设置：

```azurecli
az resource list -o table -g akvrotation
```

结果类似于以下输出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>创建和部署存储帐户密钥轮换函数

接下来，创建一个使用系统托管标识的函数应用以及其他所需组件，并部署存储帐户密钥轮换函数

函数应用轮换函数需要以下组件和配置：
- 一个 Azure 应用服务计划
- 进行函数应用触发器管理时所需的存储帐户
- 用于访问 Key Vault 中的机密的访问策略
- 将存储帐户密钥操作员服务角色分配给函数应用，用于访问存储帐户访问密钥
- 具有事件触发器和 http 触发器的存储帐户密钥轮换函数（按需轮换）
- “SecretNearExpiry”事件的 EventGrid 事件订阅

1. 选择 Azure 模板部署链接： 

   [![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. 在“资源组”列表中选择“akvrotation” 。
1. 在“存储帐户名称”中，键入包含要轮换的访问密钥的存储帐户名称
1. 在“密钥保管库名称”中，键入密钥保管库名称
1. 在“函数应用名称”中，键入函数应用名称
1. 在“机密名称”中，键入存储了访问密钥的机密的名称
1. 在“存储库 Url”中，键入函数代码 GitHub 位置 (https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git ) 
1. 选择“查看 + 创建”。
1. 选择“创建”

   ![查看并创建第一个存储帐户](../media/secrets/rotation-dual/dual-rotation-2.png)

完成上述步骤后，你将获得一个存储帐户、一个服务器场、一个函数应用、应用程序见解。 部署完成后，应会看到以下屏幕：![部署完成](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> 如果出现任何故障，可以单击“重新部署”来完成其余组件的部署。


部署模板和轮换函数代码可以在 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell) 上找到。

## <a name="add-storage-account-access-key-to-key-vault"></a>将存储帐户访问密钥添加到 Key Vault

首先，设置访问策略，以向用户授予“管理机密”权限：

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

现在，可以使用存储帐户访问密钥作为值来创建新的机密。 还需要存储帐户资源 ID、机密有效期和要添加到机密的密钥 ID，以便轮换函数可以在存储帐户中再生成密钥。

检索存储帐户资源 ID。 可在 `id` 属性下找到值
```azurecli
az storage account show -n akvrotationstorage
```

列出存储帐户访问密钥以检索密钥值

```azurecli
az storage account keys list -n akvrotationstorage 
```

填充“key1Value”和“storageAccountResourceId”的检索值

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

创建过期时间较短的机密会在几分钟内发布 `SecretNearExpiry` 事件，而该事件又会触发函数来轮换该机密。

可以通过检索和比较存储帐户密钥和保管库机密来验证是否再生成了访问密钥。

可以使用以下命令显示机密信息：
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
请注意，`CredentialId` 已更新为备用 `keyName`，并且已重新生成 `value`![第一个存储帐户的 az keyvault secret show 的输出](../media/secrets/rotation-dual/dual-rotation-4.png)

检索访问密钥以验证值
```azurecli
az storage account keys list -n akvrotationstorage 
```
![第一个存储帐户的 az storage account keys list 的输出](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>添加用于轮换的其他存储帐户

可以重复使用同一函数应用来轮换多个存储帐户。 

若要在现有功能中添加用于轮换的其他存储帐户密钥，需要：
- 将存储帐户密钥操作员服务角色分配给函数应用，用于访问存储帐户访问密钥
- “SecretNearExpiry”事件的 EventGrid 事件订阅

1. 选择 Azure 模板部署链接： 

   [![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. 在“资源组”列表中选择“akvrotation” 。
1. 在“存储帐户名称”中，键入包含要轮换的访问密钥的存储帐户名称
1. 在“密钥保管库名称”中，键入密钥保管库名称
1. 在“函数应用名称”中，键入函数应用名称
1. 在“机密名称”中，键入存储了访问密钥的机密的名称
1. 选择“查看 + 创建”。
1. 选择“创建”

   ![查看并创建第二个存储帐户](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>将其他存储帐户访问密钥添加到 Key Vault

检索存储帐户资源 ID。 可在 `id` 属性下找到值
```azurecli
az storage account show -n akvrotationstorage2
```

列出存储帐户访问密钥以检索密钥 2 值

```azurecli
az storage account keys list -n akvrotationstorage2 
```

填充“key2Value”和“storageAccountResourceId”的检索值

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

使用以下命令显示机密信息：
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
请注意，`CredentialId` 已更新为备用 `keyName`，并且已重新生成 `value`![第二个存储帐户的 az keyvault secret show 的输出](../media/secrets/rotation-dual/dual-rotation-8.png)

检索访问密钥以验证值
```azurecli
az storage account keys list -n akvrotationstorage 
```
![第二个存储帐户的 az storage account keys list 的输出](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>可用的保管库双重凭据轮换函数

- [存储帐户](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis 缓存](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>了解详细信息
- 概述：[通过 Azure 事件网格监视 Key Vault（预览版）](../general/event-grid-overview.md)
- 如何：[在 Azure 门户中创建你的第一个函数](../../azure-functions/functions-create-first-azure-function.md)
- 如何：[Key Vault 机密发生更改时接收电子邮件](../general/event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件网格事件架构（预览版）](../../event-grid/event-schema-key-vault.md)
