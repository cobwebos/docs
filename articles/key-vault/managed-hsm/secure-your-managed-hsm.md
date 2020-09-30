---
title: 保护对托管 HSM 的访问 - Azure Key Vault 托管 HSM
description: 了解如何使用 Azure RBAC 和本地托管 HSM RBAC 保护对托管 HSM 的访问
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 18ffa0f878effda8888200c13ab312851aaebdcd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992158"
---
# <a name="secure-access-to-your-managed-hsms"></a>保护对托管 HSM 的访问

Azure Key Vault 托管 HSM 是一项保护加密密钥的云服务。 因为此数据是敏感数据和业务关键数据，所以需要保护对托管 HSM 的访问，只允许得到授权的应用程序和用户进行访问。 本文简要介绍了托管 HSM 访问控制模型。 其中介绍了身份验证和授权，以及如何保护对托管 HSM 的访问。

本教程将逐步完成一个简单示例，该示例演示如何使用 Azure RBAC 和本地托管 HSM RBAC 实现职责分离和访问控制。 若要了解托管 HSM 访问控制模型，请参阅[托管 HSM 访问控制](access-control.md)。

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

若要详细了解通过 CLI 使用的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="example"></a>示例

本例中，我们正在开发一个应用程序，该应用程序使用 RSA 2048 位密钥进行签名操作。 我们的应用程序在具有[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的 Azure 虚拟机 (VM) 中运行。 用于签名的两个 RSA 密钥都存储在托管 HSM 中。

我们已确定了负责管理、部署和审核应用程序的以下角色：

- **安全团队**：CSO（首席安全官）办公室中的 IT 人员或类似参与者。 安全团队负责妥善保管密钥。 密钥包括用于签名的 RSA 或 EC 密钥，以及用于数据加密的 RSA 或 AES 密钥。
- **开发人员和操作人员**：开发应用程序并在 Azure 中进行部署的人员。 此团队的成员不属于安全人员。 他们不应有权访问 RSA 密钥等敏感数据。 仅他们部署的应用程序才应有权访问此敏感数据。
- **审核员**：此角色适用于不属于开发人员或一般 IT 人员的参与者。 他们评审证书、密钥和机密的使用及维护，确保符合安全标准。

还有一个超出我们应用程序范围的角色：订阅（或资源组）管理员。 订阅管理员为安全团队设置初始访问权限。 他们通过使用具有应用程序所需资源的资源组来授予安全团队访问权限。

我们需要为角色的以下操作进行授权：

**安全团队**
- 创建托管 HSM。
- 下载托管 HSM 安全域（用于灾难恢复）
- 启用日志。
- 生成或导入密钥
- 为灾难恢复创建托管 HSM 备份。
- 设置托管 HSM 本地 RBAC ，向用户和应用程序授予执行特定操作的权限。
- 定期更新密钥。

**开发人员和操作人员**
- 从安全团队处获取用于签名的 RSA 密钥的引用（密钥 URI）。
- 开发和部署以编程方式访问密钥的应用程序。

**审核人员**
- 查看密钥到期日期，确保密钥保持最新
- 监视角色分配，确保仅授权用户/应用程序可以访问密钥
- 查看托管 HSM 日志，确认正确使用了密钥且符合数据安全标准。

下表总结了为访问托管 HSM 而对团队和资源进行的角色分配。

| 角色 | 管理平面角色 | 数据平面角色 |
| --- | --- | --- |
| 安全团队 | 托管 HSM 参与者 | 托管 HSM 管理员 |
| 开发人员和操作员 | 无 | 无 |
| 审核人员 | 无 | 托管 HSM 加密审核者 |
| 应用程序所用 VM 的托管标识| 无 | 托管 HSM 加密用户 |
| 应用程序所用存储帐户的托管标识| 无| 托管 HSM 服务加密 |

三个团队角色需要访问其他资源的权限以及托管 HSM 权限。 若要部署 VM（或 Azure 应用服务的 Web 应用功能），开发人员和操作人员需要对这些资源类型的 `Contributor` 访问权限。 审核员需要具有对存储托管 HSM 日志的存储帐户的“读取”访问权限。

若要分配管理平面角色 (Azure RBAC)，可以使用 Azure 门户或其他任何管理接口，例如 Azure CLI 或 Azure PowerShell。 若要分配托管 HSM 数据平面角色，必须使用 Azure CLI。

本部分中的 Azure CLI 代码片段基于以下假设生成：

- Azure Active Directory 管理员已创建安全组来表示三个角色：Contoso 安全团队、Contoso 应用开发运营团队和 Contoso 应用审核员。 管理员已将用户添加到其各自的组中。
- 所有资源都位于 **ContosoAppRG** 资源组中。
- 托管 HSM 日志存储在 contosologstorage 存储帐户中。
- ContosoMHSM 托管 HSM 和 contosologstorage 存储帐户位于同一 Azure 位置 。

订阅管理员将 `Managed HSM Contributor` 角色分配给安全团队。 此角色允许安全团队管理现有的托管 HSM 以及新建托管 HSM。 如果存在现有的托管 HSM，需要为他们分配“托管 HSM 管理员”角色才能对其进行管理。

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

安全团队设置日志记录并为审核员和 VM 应用程序分配角色。

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

本教程的大部分内容仅展示与访问控制相关的操作。 为了使示例侧重于访问控制和角色管理，此处未展示与在 VM 中部署应用程序、使用客户托管密钥为存储帐户启用加密，以及创建托管 HSM 相关的其他操作。

我们的示例介绍了一个简单的方案。 现实方案可能更复杂。 可以根据需要调整密钥保管库的权限。 我们假设安全团队提供密钥和机密引用（URI 和指纹），开发运营员工在其应用程序中使用这些引用。 开发人员和操作员不需要任何数据平面访问权限。 我们将重点放在如何保护密钥保管库上。 对于保护 [VM](https://azure.microsoft.com/services/virtual-machines/security/)、[存储帐户](../../storage/blobs/security-recommendations.md)和其他 Azure 资源，应进行类似的考虑。

## <a name="resources"></a>资源

- [Azure RBAC 文档](../../role-based-access-control/overview.md)
- [Azure RBAC：内置角色](../../role-based-access-control/built-in-roles.md)
- [使用 Azure CLI 管理 Azure RBAC](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>后续步骤

有关面向管理员的入门教程，请参阅[什么是托管 HSM？](overview.md)。

若要详细了解托管 HSM 日志记录的使用情况日志记录，请参阅[托管 HSM 日志记录](logging.md)。
