---
title: Azure Key Vault 客户数据功能 | Microsoft Docs
description: 了解 Key Vault 中的客户数据
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/15/2018
ms.author: barclayn
ms.openlocfilehash: 12a176a6eba3b6dd83ef7fb97f89ecfe99083059
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213316"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault 客户数据功能

Azure Key Vault 在创建或更新保管库、密钥、机密、证书和托管的存储帐户期间接收客户数据。 此客户数据在 Azure 门户中以及通过 REST API 直接可见。 可以通过更新或删除包含客户数据的对象来编辑或删除此数据。

系统访问日志是在用户或应用程序访问 Key Vault 时生成的。 使用 Azure 见解的客户可以获得详细的访问日志。

## <a name="identifying-customer-data"></a>标识客户数据

以下信息标识 Azure Key Vault 中的客户数据：

- Azure Key Vault 的访问策略包含代表用户、组或应用程序的对象 ID
- 证书使用者可能包含电子邮件地址或者其他用户或组织标识符
- 证书联系人可能包含用户电子邮件地址、姓名或电话号码
- 证书颁发者可能包含电子邮件地址、姓名、电话号码、帐户凭据和组织详细信息
- 可以向 Azure Key Vault 中的对象应用任意标记。 这些对象包括保管库、密钥、机密、证书和存储帐户。 使用的标记可能包含个人数据
- Azure Key Vault 访问日志包含每个 REST API 调用的对象 ID、[UPN](../active-directory/connect/active-directory-aadconnect-userprincipalname.md) 和 IP 地址
- Azure Key Vault 诊断日志可能包含 REST API 调用的对象 ID 和 IP 地址

## <a name="deleting-customer-data"></a>删除客户数据

用于创建保管库、密钥、机密、证书和托管存储帐户的相同 REST API、门户体验和 SDK 也能够更新和删除这些对象。

软删除使你可以在删除后的 90 天内恢复已删除的数据。 使用软删除时，可以通过执行清除操作在 90 天保留期到期之前永久删除数据。 如果保管库或订阅已配置为阻止清除操作，则不能在计划的保留期结束前永久删除数据。

## <a name="exporting-customer-data"></a>导出客户数据

用于创建保管库、密钥、机密、证书和托管存储帐户的相同 REST API、门户体验和 SDK 也可以让你查看和导出这些对象。

Azure Key Vault 访问日志记录是可选功能，可将其打开，为每个 REST API 调用生成日志。 这些日志将被传输到订阅中的存储帐户，并且你向该订阅应用符合组织要求的保留策略。

Azure Key Vault 诊断日志包含可以通过在用户隐私门户中发出导出请求来进行检索的个人数据。 此请求必须由租户管理员发出。

## <a name="next-steps"></a>后续步骤

- [Azure 密钥保管库日志记录](key-vault-logging.md)

- [Azure Key Vault 软删除概述](key-vault-soft-delete-cli.md)

- [保管库](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Azure Key Vault 密钥操作](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault 机密操作](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault 证书和策略](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [证书颁发者](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Azure Key Vault 存储帐户操作](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
