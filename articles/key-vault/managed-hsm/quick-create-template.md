---
title: Azure 快速入门 - 使用 Azure 资源管理器模板创建托管 HSM
description: 本快速入门介绍如何使用资源管理器模板为 Azure 创建 Azure Key Vault 托管 HSM
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 4d1488d6dd2e5d08ae774ca88b7ab41b2020efe5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90998286"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建 Key Vault 托管 HSM

托管 HSM 是一项完全托管、高度可用、单租户、符合标准的云服务，通过该服务，你可以使用 FIPS 140-2 级别 3 验证的 HSM 保护云应用程序的加密密钥。  

本快速入门重点介绍部署资源管理器模板来创建托管 HSM 的过程。  [资源管理器模板](../../azure-resource-manager/templates/overview.md)是定义项目基础结构和配置的 JavaScript 对象表示法 (JSON) 文件。 该模板使用声明性语法，使你可以声明要部署的内容，而不需要编写一系列编程命令来进行创建。 若要详细了解如何开发资源管理器模板，请参阅[资源管理器文档](../../azure-resource-manager/index.yml)和[模板参考](/azure/templates/microsoft.keyvault/allversions)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，必须准备好以下项：

- Microsoft Azure 订阅。 如果没有，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
- Azure CLI 版本 2.12.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

若要详细了解通过 CLI 使用的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-manage-hsm"></a>创建托管 HSM

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/)。

模板中定义的 Azure 资源：

* **Microsoft.KeyVault/managedHSMs**：创建 Azure Key Vault 托管 HSM。

可在[此处](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)找到更多的 Azure Key Vault 模板示例。

模板需要与你的帐户关联的对象 ID。 若要找到它，请使用 Azure CLI [az ad user show](/cli/azure/ad/user?view=azure-cli-latest&preserve-view=true#az_ad_user_show) 命令，将电子邮件地址传递给 `--id` 参数。 可以使用 `--query` 参数将输出限制为仅对象 ID。

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

可能还需要租户 ID。 若要找到它，请使用 Azure CLI [az ad user show](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_show) 命令。 可以使用 `--query` 参数将输出限制为仅租户 ID。

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建 Key Vault 和机密。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. 选择或输入以下值。

    除非另有指定，否则请使用默认值创建 Key Vault 和机密。

    - 订阅：选择 Azure 订阅。
    - 资源组：选择“新建”，输入资源组的唯一名称，然后单击“确定” 。
    - 位置：选择一个位置。 例如：“美国中南部”。
    - **managedHSMName**：输入托管 HSM 的名称。
    - **SKU**：输入要创建的托管 HSM 的名称和系列。  在本快速入门中，输入“Standard_B1”作为名称，并输入“B”作为系列。
    - **租户 ID**：模板函数会自动检索租户 ID；请勿更改默认值。  如果没有值，请输入在[条件](#prerequisites)中检索到的租户 ID。
    * **initialAdminObjectIds**：输入在[条件](#prerequisites)中检索到的对象 ID。

3. 选择“购买”。 成功部署密钥保管库后，你会收到通知：

使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了托管 HSM。 此托管 HSM 在激活后才能完全正常工作。 请参阅[激活托管 HSM](quick-create-cli.md#activate-your-managed-hsm)，了解如何激活 HSM。

- 请参阅[托管 HSM 概述](overview.md)
- 了解如何[管理托管 HSM 中的密钥](key-management.md)
- 查看[托管 HSM 最佳做法](best-practices.md)