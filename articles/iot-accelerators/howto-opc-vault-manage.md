---
title: 如何管理 OPC 保管库证书管理服务-Azure |Microsoft Docs
description: 管理 OPC 保管库根 CA 证书和用户权限。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6e53914b16bc126cdab7300d918dbb7b6b868728
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973800"
---
# <a name="how-to-manage-the-opc-ua-certificate-service"></a>如何管理 OPC UA 证书服务

本文介绍了 Azure 中的 OPC UA 证书管理服务的管理任务, 如何续订颁发者 CA 证书, 如何续订证书吊销列表 (CRL) 以及如何授予和吊销用户访问权限。

## <a name="create-or-renew-the-root-ca-certificate"></a>创建或续订根 CA 证书

部署后, 创建根 CA 证书是必需的步骤。 如果没有有效的颁发者 CA 证书, 则无法签署和颁发应用程序证书。<br>请参阅[证书生存期](howto-opc-vault-secure-ca.md#certificates)一章, 以合理、安全的生存期管理证书。 颁发者 CA 证书应在其生存期的一半后续订, 但不得晚于在新签名应用程序证书的配置生存期超出颁发者证书的生存期之前。<br>
> [!IMPORTANT]
> 创建或续订颁发者 CA 证书需要 "管理员" 角色。

1. 在`https://myResourceGroup-app.azurewebsites.net`中打开证书服务, 并登录。
2. 导航到 `Certificate Groups` 页。
3. 列出了一个`Default`证书组。 单击 `Edit`。
4. 在`Edit Certificate Group Details`中, 你可以修改 CA 和应用程序证书的使用者名称和生存期。<br>主题和生存期仅应在颁发第一个 CA 证书之前设置一次。 操作期间的生存期更改可能会导致所颁发证书和 Crl 的生存期不一致。
5. 输入有效主题, 例如`CN=My CA Root, O=MyCompany, OU=MyDepartment`。<br>
   > [!IMPORTANT]
   > 更改使用者需要续订颁发者证书, 否则服务将无法对应用程序证书进行签名。 根据活动颁发者证书的使用者检查配置的主题。 如果主题不匹配, 则拒绝证书签名。
6. 单击该`Save`按钮。
7. 如果此时遇到 "禁止" 错误, 则用户凭据不具有修改或创建新根证书的管理员权限。 默认情况下, 部署该服务的用户具有该服务的管理员和签名角色, 需要将其他用户添加到 AzureAD 应用程序注册中适当的 "审批者"、"Writer" 或 "Administrator" 角色。
8. 单击该`Details`按钮。 `View Certificate Group Details`应显示更新的信息。
9. 单击该`Renew CA Certificate`按钮可颁发第一个颁发者 CA 证书或续订颁发者证书。 按`Ok`继续。
10. 几秒钟后, `Certificate Details`会显示。 按`Issuer` 或`Crl`下载最新的 CA 证书和 CRL, 以便分发到 OPC UA 应用程序。
11. 现在, OPC UA 证书管理服务已准备好颁发 OPC UA 应用程序的证书。

## <a name="renew-the-crl"></a>续订 CRL

证书吊销列表 (CRL) 的续订是更新, 应定期分发到应用程序。 OPC UA 设备支持 CRL 分发点 X509 扩展, 可以直接从微服务终结点更新 CRL。 其他 OPC UA 设备可能需要手动更新, 或者在最佳情况下, 可以使用 GDS server 推送扩展 (*) 进行更新, 以使用证书和 Crl 更新信任列表。

在以下工作流中, 在已删除状态的所有证书请求都将在 Crl 中吊销, 这对应于颁发它们的颁发者 CA 证书。 CRL 的版本号递增1。 <br>
> [!NOTE]
> 所有颁发的 Crl 都有效, 直到颁发者 CA 证书到期, 因为 OPC UA 规范不需要 CRL 的强制确定性分布模型。

> [!IMPORTANT]
> 续订颁发者 CRL 需要 "管理员" 角色。

1. 在`https://myResourceGroup.azurewebsites.net`中打开证书服务, 并登录。
2. 导航到 `Certificate Groups` 页。
3. 单击该`Details`按钮。 `View Certificate Group Details`应显示当前证书和 CRL 信息。
4. 单击该`Update CRL Revocation List(CRL)`按钮, 为 OPC 保管库存储中的所有活动颁发者证书颁发已更新的 CRL。
5. 几秒钟后, `Certificate Details`会显示。 按`Issuer` 或`Crl`下载最新的 CA 证书和 CRL, 以便分发到 OPC UA 应用程序。

## <a name="manage-user-roles"></a>管理用户角色

OPC 保管库的用户角色微服务在 Azure Active Directory 企业应用程序中进行管理。

有关角色定义的详细说明, 请参阅[Roles](howto-opc-vault-secure-ca.md#roles)部分。

默认情况下, 租户中经过身份验证的用户可以将服务作为 "读卡器" 登录。 高特权角色需要在 Azure 门户中或使用 Powershell 进行手动管理。

### <a name="add-user"></a>添加用户

1. 在处`portal.azure.com`打开 Azure 门户。
2. 导航到 `Azure Active Directory`/`Enterprise applications`。
3. 默认情况下`resourceGroupName-service`, 选择注册 OPC 保管库微服务。
4. 导航到 `Users and Groups` 。
5. 单击 `Add User`。
6. 选择或邀请用户分配到特定角色。
7. 选择用户的角色。
8. `Assign`按按钮。
9. 对于或`Administrator` `Approver`角色中的用户, 请继续添加 Azure Key Vault 访问策略。

### <a name="remove-user"></a>删除用户

1. 在处`portal.azure.com`打开 Azure 门户。
2. 导航到 `Azure Active Directory`/`Enterprise applications`。
3. 默认情况下`resourceGroupName-service`, 选择注册 OPC 保管库微服务。
4. 导航到 `Users and Groups` 。
5. 选择要删除角色的用户。
6. `Remove`按按钮。
7. 同时从 Azure Key Vault 策略删除删除的管理员和审批者。

### <a name="add-user-access-policy-to-azure-key-vault"></a>将用户访问策略添加到 Azure Key Vault

**审批者**和**管理员**需要其他访问策略。

默认情况下, 服务标识仅具有访问 Key Vault 的有限权限, 以防止在没有用户模拟的情况下进行提升的操作或更改。 基本服务权限`Get`为, `List`同时用于机密和证书。 对于机密, 只有一个例外, 用户接受密钥后, `Delete`该服务可以使用私钥。 所有其他操作都需要用户模拟的权限。<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>对于**审批者角色**, 必须将以下权限添加到 Key Vault:

1. 在处`portal.azure.com`打开 Azure 门户。
2. 导航到部署过程中`resourceGroupName`使用的 OPC 保管库。
3. 导航到 Key Vault `resourceGroupName-xxxxx`。
4. 导航到`Access Policies`。
5. 单击 `Add new`。
6. 跳过模板, 没有模板符合要求。
7. `Select Principal`单击并选择要添加的用户或邀请新用户加入租户。
8. 检查`Key permissions` :`Get`和最重要`Sign`的。 `List`
9. 检查`Secret permissions`: `Get`、 `List`和。`Delete` `Set`
10. 检查`Certificate permissions`: `Get`和。`List`
11. 单击 `Ok`。
12. `Save`变化.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>对于**管理员角色**, 必须将以下权限添加到 Key Vault:

1. 在处`portal.azure.com`打开 Azure 门户。
2. 导航到部署过程中`resourceGroupName`使用的 OPC 保管库。
3. 导航到 Key Vault `resourceGroupName-xxxxx`。
4. 导航到`Access Policies`。
5. 单击 `Add new`。
6. 跳过模板, 没有模板符合要求。
7. `Select Principal`单击并选择要添加的用户或邀请新用户加入租户。
8. 检查`Key permissions` :`Get`和最重要`Sign`的。 `List`
9. 检查`Secret permissions`: `Get`、 `List`和。`Delete` `Set`
10. 检查`Certificate permissions`: `Get`、 `List`、 `Update`和。 `Create` `Import`
11. 单击 `Ok`。
12. `Save`变化.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>从 Azure Key Vault 删除用户访问策略

1. 在处`portal.azure.com`打开 Azure 门户。
2. 导航到部署过程中`resourceGroupName`使用的 OPC 保管库。
3. 导航到 Key Vault `resourceGroupName-xxxxx`。
4. 导航到`Access Policies`。
5. 查找要删除的用户, 然后单击`... / Delete` "删除" 以删除用户访问权限。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何管理 OPC 保管库证书和用户, 下面是建议的后续步骤:

> [!div class="nextstepaction"]
> [安全 OPC 设备的通信](howto-opc-vault-secure.md)
