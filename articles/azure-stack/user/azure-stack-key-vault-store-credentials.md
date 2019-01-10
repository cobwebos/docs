---
title: Azure Stack 应用商店服务主体凭据在密钥保管库 |Microsoft Docs
description: 了解密钥保管库基于 Azure Stack 存储服务主体凭据的方式
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: sethm
ms.openlocfilehash: 570c1adc2f4615e78cbe5656c13b0e22b863baf7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192395"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>将服务主体凭据存储在密钥保管库

开发 Azure Stack 上的应用程序通常需要创建服务主体以及使用这些凭据部署之前进行身份验证。 但是，通常为服务主体的存储的凭据放错位置。 本文介绍如何创建服务主体，并在 Azure Key Vault 中存储的值，以供将来检索。

有关密钥保管库的详细信息，请参阅[这篇文章](azure-stack-key-vault-intro.md)。

## <a name="prerequisites"></a>必备组件

- 对产品/服务，包括 Azure Key Vault 服务的订阅。
- 用于 Azure Stack 配置 PowerShell。

## <a name="key-vault-in-azure-stack"></a>Azure Stack 中的密钥保管库

在 Azure Stack 中的密钥保管库有助于保护加密密钥和机密的云应用程序和服务使用。 通过使用密钥保管库，可以加密密钥和机密。

若要创建密钥保管库，请执行以下步骤：

1. 登录到 Azure Stack 门户。

2. 从仪表板中，选择 **+ 创建资源**，然后**安全性 + 标识**，然后选择**密钥保管库。**

   ![创建 Key Vault](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. 在“创建密钥保管库”窗格中，为保管库分配**名称**。 保管库名称可以包含字母数字字符和连字符 （-） 字符。 它们不应以数字开头。

4. 从可用订阅的列表中选择一个订阅。

5. 选择现有资源组或创建一个新。

6. 选择定价层。

7. 选择一个现有的访问策略或创建一个新。 访问策略，可授予权限的用户、 应用程序或安全组来对此保管库执行操作。

8. （可选） 选择高级的访问策略以允许访问功能。

9. 配置设置后，请选择“确定”，然后选择“创建”。 密钥保管库部署开始。

## <a name="create-a-service-principal"></a>创建服务主体

1. 登录到 Azure 帐户通过 Azure 门户。

2. 选择**Azure Active Directory**，然后**应用注册**，然后**添加**。

3. 为应用提供名称和 URL。 选择“Web 应用/API”或“本机”作为要创建的应用程序的类型。 设置这些值后，选择“创建”。

4. 选择**Active Directory**，然后**应用注册**，然后选择你的应用程序。

5. 复制“应用程序 ID”并将其存储在应用程序代码中。 在示例应用程序中的应用程序使用**客户端 ID**指**应用程序 ID**。

6. 若要生成身份验证密钥，请选择“密钥”。

7. 针对密钥和持续时间提供的说明。

8. 选择“保存”。

9. 复制**键**该按钮将变为可用后，单击**保存**。

## <a name="store-the-service-principal-inside-key-vault"></a>存储在密钥保管库内的服务主体

1. 为 Azure Stack 中，登录到用户门户，然后选择之前创建的密钥保管库，然后选择**机密**磁贴。

2. 在中**机密**窗格中，选择**生成/导入**。

3. 在中**创建机密**窗格中，从列表中的选项选择**手动**。

4. 输入**应用程序 ID**复制的服务主体中作为你的密钥的名称。 项名称可以包含字母数字字符和连字符 （-） 字符。

5. 从服务主体到你的密钥值粘贴到**值**选项卡。

6. 选择**服务主体**有关**内容类型**。

7. 设置**激活日期**并**到期日期**密钥值。

8. 选择“创建”以开始部署。

已成功创建机密后，服务主体信息将存储在此处。 可以在任何时候下选择**机密**，并查看或修改其属性。 属性部分包含机密标识符，即外部应用程序用于访问此机密统一资源标识符 (URI)。

## <a name="next-steps"></a>后续步骤

- [使用服务主体](azure-stack-create-service-principals.md)
- [通过门户管理 Azure Stack 中的密钥保管库](azure-stack-key-vault-manage-portal.md)  
- [使用 PowerShell 管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-powershell.md)