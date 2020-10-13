---
title: 快速入门 - 预配和激活 Azure 托管 HSM
description: 演示如何使用 Azure CLI 预配和激活托管 HSM 的快速入门
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756805"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>快速入门：使用 Azure CLI 预配和激活托管 HSM

Azure Key Vault 托管 HSM 是一项完全托管、高度可用、单租户且符合标准的云服务，你可用它来使用 FIPS 140-2 级别 3 验证的 HSM 保护云应用程序的加密密钥。 要详细了解托管 HSM，可参阅[概述](overview.md)。 

在本快速入门中，你将使用 Azure CLI 创建和激活托管 HSM。 完成该操作后，即可存储机密。

## <a name="prerequisites"></a>必备知识

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

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例会在 eastus2 位置创建一个名为 ContosoResourceGroup 的资源组。 

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>创建托管 HSM

创建托管 HSM 的过程分为两步：
1. 预配“托管 HSM”资源。
1. 下载安全域来激活托管 HSM。

### <a name="provision-a-managed-hsm"></a>预配托管 HSM

使用 `az keyvault create` 命令创建托管 HSM。 此脚本包含 3 个必需参数：资源组名称、HSM 名称和地理位置。

需提供以下输入才能创建“托管 HSM”资源：
- 要将其放置在订阅中的资源组。
- Azure 位置。
- 初始管理员的列表。

以下示例会在资源组 ContosoResourceGroup（位于美国东部 2 位置）创建一个名为 ContosoMHSM 的 HSM，并将当前已登录的用户设为唯一管理员   。

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> 运行 create 命令可能需要几分钟的时间。 它成功返回输出后，便可激活 HSM。

此命令的输出会显示创建的托管 HSM 的属性。 两个最重要的属性是：

* **名称**：在本示例中，名称为 ContosoMHSM。 将在其他 Key Vault 命令中使用此名称。
* **hsmUri**：在本示例中，URI 为“https://contosohsm.managedhsm.azure.net”。 通过其 REST API 使用 HSM 的应用程序必须使用此 URI。

Azure 帐户现已获得授权，可在此托管 HSM 上执行任何作业。 到目前为止，尚未授权其他任何人。

### <a name="activate-your-managed-hsm"></a>激活托管 HSM

在激活 HSM 之前，所有数据平面命令都处于禁用状态。 你将无法创建密钥或分配角色。 只有在 create 命令期间分配的指定管理员才能激活 HSM。 必须下载[安全域](security-domain.md)才能激活 HSM。

若要激活 HSM，你需要：
- 至少 3 个 RSA 密钥对（最多 10 个）
- 指定解密安全域所需的最小密钥数（仲裁）

若要激活 HSM，请向 HSM 发送至少 3 个（最多 10 个）RSA 公钥。 HSM 利用这些密钥对安全域进行加密，并将其发回。 一旦成功完成此安全域下载，HSM 便可使用。 还需要指定仲裁，即解密安全域所需的最小私钥数。

下面的示例演示如何使用 `openssl` 生成 3 个自签名证书。

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> 安全地创建并存储在此步骤中生成的 RSA 密钥对和安全域文件。

使用 `az keyvault security-domain download` 命令下载安全域并激活托管 HSM。 下面的示例使用 3 个 RSA 密钥对（此命令只需要公钥），并将仲裁设置为 2。

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

请安全地存储安全域文件和 RSA 密钥对。 你需要它们进行灾难恢复，还需要用它们再创建一个共享同一安全域的托管 HSM，这样它们才可共享密钥。

成功下载安全域后，HSM 将处于活动状态并可供使用。

## <a name="clean-up-resources"></a>清理资源

本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。

如果不再需要资源组和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。 可以删除资源，如下所示：

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 Key Vault 并在其中存储了一个机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 请参阅[托管 HSM 概述](overview.md)
- 了解如何[管理托管 HSM 中的密钥](key-management.md)
- 查看[托管 HSM 最佳做法](best-practices.md)
