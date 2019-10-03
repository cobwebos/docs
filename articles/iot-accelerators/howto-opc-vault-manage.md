---
title: 如何管理 OPC 保管库证书服务-Azure |Microsoft Docs
description: 管理 OPC 保管库根 CA 证书和用户权限。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203652"
---
# <a name="manage-the-opc-vault-certificate-service"></a>管理 OPC 保管库证书服务

本文介绍了 Azure 中的 OPC 保管库证书管理服务的管理任务。 它包括有关如何续订颁发者 CA 证书、如何续订证书吊销列表（CRL）以及如何授予和吊销用户访问权限的信息。

## <a name="create-or-renew-the-root-ca-certificate"></a>创建或续订根 CA 证书

部署 OPC 保管库之后，必须创建根 CA 证书。 如果没有有效的颁发者 CA 证书，则无法签署或颁发应用程序证书。 请参阅[证书](howto-opc-vault-secure-ca.md#certificates)，以在安全的合理有效期下管理证书。 在有效期一半后续订颁发者 CA 证书。 续订时，还应考虑到最近签署的应用程序证书的配置生存期不应超过颁发者 CA 证书的生存期。
> [!IMPORTANT]
> 创建或续订颁发者 CA 证书需要管理员角色。

1. 在处`https://myResourceGroup-app.azurewebsites.net`打开证书服务，并登录。
2. 中转到 "**证书组**"。
3. 列出了一个默认证书组。 选择“编辑”。
4. 在 "**编辑证书组详细信息**" 中，可以修改 CA 和应用程序证书的使用者名称和生存期。 主题和生存期仅应在颁发第一个 CA 证书之前设置一次。 操作期间的生存期更改可能会导致所颁发证书和 Crl 的生存期不一致。
5. 输入有效的主题（例如`CN=My CA Root, O=MyCompany, OU=MyDepartment`）。<br>
   > [!IMPORTANT]
   > 如果更改了使用者，则必须续订颁发者证书，否则服务将无法对应用程序证书进行签名。 对照活动颁发者证书的使用者检查配置的主题。 如果主题不匹配，则拒绝证书签名。
6. 选择“保存”。
7. 如果此时遇到 "禁止" 错误，则用户凭据无权修改或创建新的根证书。 默认情况下，部署该服务的用户具有该服务的管理员和签名角色。 需要将其他用户添加到审批者、作者或管理员角色，如 Azure Active Directory （Azure AD）应用程序注册中所示。
8. 选择 "**详细信息**"。 这应显示更新的信息。
9. 选择 "**续订 CA 证书**" 颁发第一个颁发者 CA 证书，或续订颁发者证书。 然后选择“确定”。
10. 几秒钟后，你将看到**证书详细信息**。 若要下载最新的 CA 证书和 CRL 以便分发到 OPC UA 应用程序，请选择 "**颁发者**或**crl**"。

现在，OPC UA 证书管理服务已准备好颁发 OPC UA 应用程序的证书。

## <a name="renew-the-crl"></a>续订 CRL

CRL 续订是一个更新，应定期分发到应用程序。 OPC UA 设备支持 CRL 分发点 X509 扩展，可以直接从微服务终结点更新 CRL。 其他 OPC UA 设备可能需要手动更新，或可通过使用 GDS server 推送扩展（*）进行更新，以使用证书和 Crl 更新信任列表。

在以下工作流中，在已删除状态的所有证书请求都将在 Crl 中吊销，这对应于颁发它们的颁发者 CA 证书。 CRL 的版本号递增1。 <br>
> [!NOTE]
> 所有颁发的 Crl 都有效，直到颁发者 CA 证书到期。 这是因为 OPC UA 规范不需要针对 CRL 的强制确定性分布模型。

> [!IMPORTANT]
> 需要管理员角色才能续订颁发者 CRL。

1. 在处`https://myResourceGroup.azurewebsites.net`打开证书服务，并登录。
2. 中转到 "**证书组**" 页。
3. 选择 "**详细信息**"。 这应显示当前证书和 CRL 信息。
4. 选择 "**更新 Crl 吊销列表（CRL）** "，为 OPC 保管库存储中的所有活动颁发者证书颁发已更新的 crl。
5. 几秒钟后，你将看到**证书详细信息**。 若要下载最新的 CA 证书和 CRL 以便分发到 OPC UA 应用程序，请选择 "**颁发者**或**crl**"。

## <a name="manage-user-roles"></a>管理用户角色

在 Azure AD 企业应用程序中管理 OPC 保管库微服务的用户角色。 有关角色定义的详细说明，请参阅[角色](howto-opc-vault-secure-ca.md#roles)。

默认情况下，租户中经过身份验证的用户可以作为读者登录服务。 高特权角色需要在 Azure 门户中或使用 PowerShell 进行手动管理。

### <a name="add-user"></a>添加用户

1. 打开 Azure 门户。
2. 请参阅**Azure Active Directory** > **企业应用程序**。
3. 选择注册 OPC 保管库微服务（默认情况下`resourceGroupName-service`为）。
4. 请参阅 "**用户和组**"。
5. 选择“添加用户”。
6. 选择或邀请用户分配到特定角色。
7. 选择用户的角色。
8. 选择“分配”。
9. 对于 "管理员" 或 "审批者" 角色中的用户，请继续添加 Azure Key Vault 访问策略。

### <a name="remove-user"></a>删除用户

1. 打开 Azure 门户。
2. 请参阅**Azure Active Directory** > **企业应用程序**。
3. 选择注册 OPC 保管库微服务（默认情况下`resourceGroupName-service`为）。
4. 请参阅 "**用户和组**"。
5. 选择要删除角色的用户，然后选择 "**删除**"。
6. 对于 "管理员" 或 "审批者" 角色中的已删除用户，还将其从 Azure Key Vault 策略中删除。

### <a name="add-user-access-policy-to-azure-key-vault"></a>将用户访问策略添加到 Azure Key Vault

审批者和管理员需要其他访问策略。

默认情况下，服务标识仅具有访问 Key Vault 的有限权限，以防止在没有用户模拟的情况下进行提升的操作或更改。 基本服务权限为 "获取" 和 "列出"，同时用于机密和证书。 对于机密，只有一个例外：服务可在用户接受密钥后从机密存储中删除私钥。 所有其他操作都需要用户模拟的权限。

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>对于审批者角色，必须将以下权限添加到 Key Vault

1. 打开 Azure 门户。
2. 在部署过程中， `resourceGroupName`请使用 OPC 保管库。
3. 请参阅 Key Vault `resourceGroupName-xxxxx`。
4. 中转到 "**访问策略**"。
5. 选择“添加新订阅”。
6. 跳过模板。 没有符合要求的模板。
7. 选择 "**选择主体**"，然后选择要添加的用户，或邀请新用户加入租户。
8. 选择以下**密钥权限**：**获取**、**列出**和**签名**。
9. 选择以下**机密权限**：**获取**、**列出**、**设置**和**删除**。
10. 选择以下**证书权限**：**Get**和**List**。
11. 选择 **"确定"** ，然后选择 "**保存**"。

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>对于管理员角色，必须将以下权限添加到 Key Vault

1. 打开 Azure 门户。
2. 在部署过程中， `resourceGroupName`请使用 OPC 保管库。
3. 请参阅 Key Vault `resourceGroupName-xxxxx`。
4. 中转到 "**访问策略**"。
5. 选择“添加新订阅”。
6. 跳过模板。 没有符合要求的模板。
7. 选择 "**选择主体**"，然后选择要添加的用户，或邀请新用户加入租户。
8. 选择以下**密钥权限**：**获取**、**列出**和**签名**。
9. 选择以下**机密权限**：**获取**、**列出**、**设置**和**删除**。
10. 选择以下**证书权限**：**获取**、**列出**、**更新**、**创建**和**导入**。
11. 选择 **"确定"** ，然后选择 "**保存**"。

### <a name="remove-user-access-policy-from-azure-key-vault"></a>从 Azure Key Vault 删除用户访问策略

1. 打开 Azure 门户。
2. 在部署过程中， `resourceGroupName`请使用 OPC 保管库。
3. 请参阅 Key Vault `resourceGroupName-xxxxx`。
4. 中转到 "**访问策略**"。
5. 查找要删除的用户，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何管理 OPC 保管库证书和用户，可以：

> [!div class="nextstepaction"]
> [安全 OPC 设备的通信](howto-opc-vault-secure.md)
