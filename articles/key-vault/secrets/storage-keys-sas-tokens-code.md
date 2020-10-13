---
title: 通过编写代码提取共享访问签名令牌 | Azure Key Vault
description: 托管存储帐户功能在 Azure Key Vault 与 Azure 存储帐户之间提供无缝集成。 此示例使用 Azure SDK for .NET 管理 SAS 令牌。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 6072c71fa675bd203e94f3f42814a1183b12ae8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597975"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>创建 SAS 定义，并通过编写代码提取共享访问签名令牌

你可以使用存储在密钥保险库中的共享访问签名 (SAS) 令牌来管理存储帐户。 有关详细信息，请参阅[使用 SAS 授予对 Azure 存储资源的有限访问权限](../../storage/common/storage-sas-overview.md)。

> [!NOTE]
> 建议使用[基于角色的访问控制(RBAC)](../../storage/common/storage-auth-aad.md) 来保护存储帐户，以获得比“共享密钥”授权更高的安全性和易用性。

本文展示了用于创建 SAS 定义和获取 SAS 令牌的 .NET 代码的示例。 请参阅我们的 [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) 示例，获取完整详细信息，包括为密钥保管库托管的存储帐户生成的客户端的信息。 有关如何创建和存储 SAS 令牌的信息，请参阅[使用密钥保管库和 Azure CLI 管理存储帐户密钥](overview-storage-keys.md)或[使用密钥保管库和 Azure PowerShell 管理存储帐户密钥](overview-storage-keys-powershell.md)。

## <a name="code-samples"></a>代码示例

在以下示例中，将创建 SAS 模板：

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

使用此模板，可以通过使用...创建 SAS 定义 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

创建 SAS 定义后，就可以使用 `SecretClient` 检索 SAS 令牌，比如机密。 需要在机密名称前面加上存储帐户名称，后跟短划线：

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

如果共享访问签名令牌即将过期，可以再次获取相同的机密并生成新的令牌。

要了解如何使用从 Key Vault SAS 中检索的令牌访问 Azure 存储服务，请参阅[使用帐户 SAS 访问 Blob 服务](https://docs.microsoft.com/azure/storage/common/storage-account-sas-create-dotnet#use-an-account-sas-from-a-client)

> [!NOTE]
> 如果应用从存储收到 403，则应用需要准备好刷新 SAS，使你能够处理密钥被泄露的情况，并且轮替操作需要比正常轮替期更频繁。 

## <a name="next-steps"></a>后续步骤
- 了解如何[使用 SAS 授予对 Azure 存储资源的有限访问权限](../../storage/common/storage-sas-overview.md)。
- 了解如何[使用密钥保管库和 Azure CLI 管理存储帐户密钥](overview-storage-keys.md)或 [Azure PowerShell](overview-storage-keys-powershell.md)。
- 请参阅[托管存储帐户密钥示例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
