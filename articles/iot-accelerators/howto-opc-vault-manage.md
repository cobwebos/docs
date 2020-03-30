---
title: 如何管理 OPC 保管库证书服务 - Azure |微软文档
description: 管理 OPC 保管库根 CA 证书和用户权限。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203652"
---
# <a name="manage-the-opc-vault-certificate-service"></a>管理 OPC 保管库证书服务

本文介绍了 Azure 中 OPC 保管库证书管理服务的管理任务。 它包括有关如何续订颁发者 CA 证书、如何续订证书吊销列表 （CRL） 以及如何授予和撤销用户访问权限的信息。

## <a name="create-or-renew-the-root-ca-certificate"></a>创建或续订根 CA 证书

部署 OPC 保管库后，必须创建根 CA 证书。 如果没有有效的颁发者 CA 证书，则无法签名或颁发应用程序证书。 请参阅[证书](howto-opc-vault-secure-ca.md#certificates)以管理具有合理、安全生存期的证书。 在颁发者 CA 证书的一半生存期后续订它。 续订时，还要考虑新签名的应用程序证书的配置生存期不应超过颁发者 CA 证书的生存期。
> [!IMPORTANT]
> 创建或续订颁发者 CA 证书需要管理员角色。

1. 在 打开证书服务`https://myResourceGroup-app.azurewebsites.net`，然后登录。
2. 转到**证书组**。
3. 列出了一个默认证书组。 选择 **"编辑**"。
4. 在 **"编辑证书组详细信息"** 中，可以修改 CA 和应用程序证书的主题名称和生存期。 在颁发第一个 CA 证书之前，应仅设置主题和生存期一次。 操作期间的生存期更改可能会导致颁发的证书和 CRL 的生存期不一致。
5. 输入有效的主题（例如， `CN=My CA Root, O=MyCompany, OU=MyDepartment`<br>
   > [!IMPORTANT]
   > 如果更改主题，则必须续订颁发者证书，否则服务将无法对应用程序证书进行签名。 配置的主题根据活动颁发者证书的主题进行检查。 如果主题不匹配，证书签名将被拒绝。
6. 选择“保存”。****
7. 如果此时遇到"禁止"错误，则用户凭据没有管理员修改或创建新根证书的权限。 默认情况下，部署服务的用户具有管理员并与服务签名的角色。 需要在 Azure 活动目录 （Azure AD） 应用程序注册中根据需要将其他用户添加到审批者、编写者或管理员角色。
8. 选择**详细信息**。 这将显示更新的信息。
9. 选择**续订 CA 证书**以颁发第一个颁发者 CA 证书，或续订颁发者证书。 然后选择 **"确定**"。
10. 几秒钟后，您将看到**证书详细信息**。 要下载最新的 CA 证书和 CRL 以分发到 OPC UA 应用程序，请选择 **"颁发者**"或 **"Crl**"。

现在，OPC UA 证书管理服务已准备好为 OPC UA 应用程序颁发证书。

## <a name="renew-the-crl"></a>续订 CRL

CRL 的更新是一种更新，应定期分发给应用程序。 支持 CRL 分发点 X509 扩展的 OPC UA 设备可以直接从微服务终结点更新 CRL。 其他 OPC UA 设备可能需要手动更新，或者可以使用 GDS 服务器推送扩展 （*） 更新信任列表与证书和 CRL。

在以下工作流中，已删除状态中的所有证书请求都将在 CRL 中撤消，这些证书对应于为其发出的颁发证书的颁发者 CA 证书。 CRL 的版本号增加 1。 <br>
> [!NOTE]
> 所有颁发的 CRL 在颁发者 CA 证书到期之前都有效。 这是因为 OPC UA 规范不需要 CRL 的强制性确定性分布模型。

> [!IMPORTANT]
> 续订颁发者 CRL 需要管理员角色。

1. 在 打开证书服务`https://myResourceGroup.azurewebsites.net`，然后登录。
2. 转到**证书组**页面。
3. 选择**详细信息**。 这应显示当前证书和 CRL 信息。
4. 选择 **"更新 CRL 吊销列表 （CRL）"，** 以便为 OPC 保管库存储中的所有活动颁发者证书颁发更新的 CRL。
5. 几秒钟后，您将看到**证书详细信息**。 要下载最新的 CA 证书和 CRL 以分发到 OPC UA 应用程序，请选择 **"颁发者**"或 **"Crl**"。

## <a name="manage-user-roles"></a>管理用户角色

在 Azure AD 企业应用程序中管理 OPC Vault 微服务的用户角色。 有关角色定义的详细说明，请参阅[角色](howto-opc-vault-secure-ca.md#roles)。

默认情况下，租户中的经过身份验证的用户可以以 Reader 身份登录服务。 较高的特权角色需要在 Azure 门户中手动管理，或者使用 PowerShell。

### <a name="add-user"></a>添加用户

1. 打开 Azure 门户。
2. 转到**Azure 活动目录** > **企业应用程序**。
3. 选择 OPC Vault 微服务的注册（默认情况下，您的`resourceGroupName-service`）。
4. 转到**用户和组**。
5. 选择 **"添加用户**"。
6. 选择或邀请用户分配到特定角色。
7. 选择用户的角色。
8. 选择“分配”。****
9. 对于管理员或审批者角色中的用户，请继续添加 Azure 密钥保管库访问策略。

### <a name="remove-user"></a>删除用户

1. 打开 Azure 门户。
2. 转到**Azure 活动目录** > **企业应用程序**。
3. 选择 OPC Vault 微服务的注册（默认情况下，您的`resourceGroupName-service`）。
4. 转到**用户和组**。
5. 选择要删除的角色的用户，然后选择 **"删除**"。
6. 对于管理员或审批者角色中的已删除用户，还要从 Azure 密钥保管库策略中删除这些用户。

### <a name="add-user-access-policy-to-azure-key-vault"></a>将用户访问策略添加到 Azure 密钥保管库

审批者和管理员需要其他访问策略。

默认情况下，服务标识访问密钥保管库的权限有限，以防止在没有用户模拟的情况下进行提升的操作或更改。 对于机密和证书，基本服务权限为"获取"和"列表"。 对于机密，只有一个例外：服务可以在用户接受密钥后从密钥存储中删除它。 所有其他操作都需要用户模拟权限。

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>对于审批者角色，必须将以下权限添加到密钥保管库

1. 打开 Azure 门户。
2. 转到部署期间使用的 OPC 保管库`resourceGroupName`。
3. 转到密钥保管库`resourceGroupName-xxxxx`。
4. 转到**访问策略**。
5. 选择“添加新订阅”****。
6. 跳过模板。 没有符合要求的模板。
7. **选择"选择主体**"，然后选择要添加的用户，或邀请新用户到租户。
8. 选择以下**密钥权限**：**获取**、**列出**和**签名**。
9. 选择以下**机密权限**：**获取**、**列表**、**设置**和**删除**。
10. 选择以下**证书权限**：**获取**和**列出**。
11. 选择 **"确定**"，然后选择 **"保存**"。

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>对于管理员角色，必须将以下权限添加到密钥保管库

1. 打开 Azure 门户。
2. 转到部署期间使用的 OPC 保管库`resourceGroupName`。
3. 转到密钥保管库`resourceGroupName-xxxxx`。
4. 转到**访问策略**。
5. 选择“添加新订阅”****。
6. 跳过模板。 没有符合要求的模板。
7. **选择"选择主体**"，然后选择要添加的用户，或邀请新用户到租户。
8. 选择以下**密钥权限**：**获取**、**列出**和**签名**。
9. 选择以下**机密权限**：**获取**、**列表**、**设置**和**删除**。
10. 选择以下**证书权限**：**获取**、**列表**、**更新**、**创建**和**导入**。
11. 选择 **"确定**"，然后选择 **"保存**"。

### <a name="remove-user-access-policy-from-azure-key-vault"></a>从 Azure 密钥保管库中删除用户访问策略

1. 打开 Azure 门户。
2. 转到部署期间使用的 OPC 保管库`resourceGroupName`。
3. 转到密钥保管库`resourceGroupName-xxxxx`。
4. 转到**访问策略**。
5. 查找要删除的用户，然后选择 **"删除**"。

## <a name="next-steps"></a>后续步骤

现在，您已经了解如何管理 OPC Vault 证书和用户，您可以：

> [!div class="nextstepaction"]
> [OPC 设备的安全通信](howto-opc-vault-secure.md)
