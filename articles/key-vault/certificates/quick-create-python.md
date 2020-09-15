---
title: 快速入门 – Azure Key Vault Python 客户端库 – 管理证书
description: 了解如何使用 Python 客户端库在 Azure Key Vault 中创建、检索和删除证书
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488620"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>快速入门：适用于 Python 的 Azure Key Vault 证书客户端库

适用于 Python 的 Azure Key Vault 客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。 通过使用 Key Vault 存储证书，可以避免在代码中存储证书，从而提高应用的安全性。

[API 参考文档](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [包（Python 包索引）](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>设置本地环境

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. 安装 Key Vault 证书库：

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>为服务主体授予对 Key Vault 的访问权限

运行以下 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，以授权服务主体对证书进行获取、列出和创建操作。

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

此命令依赖前面步骤中创建的 `KEY_VAULT_NAME` 和 `AZURE_CLIENT_ID` 环境变量。

有关详细信息，请参阅[分配访问策略 - CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>创建示例代码

使用适用于 Python 的 Azure Key Vault 客户端库，可以管理证书和相关的资产（例如机密和加密密钥）。 以下代码示例演示如何创建客户端以及设置、检索和删除机密。

创建包含此代码的名为 kv_certificates.py 的文件。

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>运行代码

确保上一部分中的代码位于名为 kv_certificates.py 的文件中。 然后，使用以下命令运行代码：

```terminal
python kv_certificates.py
```

- 如果遇到权限错误，请确保已运行 [`az keyvault set-policy` 命令](#give-the-service-principal-access-to-your-key-vault)。
- 重新运行具有相同密钥名称的代码可能会产生错误：“(冲突)证书 <name> 当前处于已删除但可恢复的状态。” 使用其他密钥名称。

## <a name="code-details"></a>代码详细信息

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

在前面的代码中，[`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) 对象使用针对服务主体创建的环境变量。 每当从 Azure 库创建客户端对象（例如 [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python)）以及要通过该客户端使用的资源的 URI 时，都要提供此凭据：

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>保存证书

获取密钥保管库的客户端对象后，可以使用 [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) 方法来创建证书： 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

此处，证书要求使用 [CertificatePolicy get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--) 方法来获取策略。

调用 `begin_create_certificate` 方法会生成对密钥保管库的 Azure REST API 的异步调用。 异步调用会返回一个轮询器对象。 若要等待操作的结果，请调用轮询器的 `result` 方法。

在处理请求时，Azure 使用你提供给客户端的凭据对象，对调用方的标识（服务主体）进行身份验证。

它还将检查调用方是否有权执行请求的操作。 先前使用 [`az keyvault set-policy` 命令](#give-the-service-principal-access-to-your-key-vault)向服务主体授予了此授权。

### <a name="retrieve-a-certificate"></a>检索证书

若要从 Key Vault 读取证书，请使用 [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-) 方法：

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

还可以使用 Azure CLI 命令 [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) 来验证是否设置了证书。

### <a name="delete-a-certificate"></a>删除证书

若要删除证书，请使用 [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-) 方法：

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate` 方法是异步方法，将返回一个轮询器对象。 调用轮询器的 `result` 方法等待其完成。

可以使用 Azure CLI 命令 [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) 来验证是否已删除证书。

证书删除后，会在一段时间内保持已删除但可恢复状态。 如果再次运行该代码，请使用其他证书名称。

## <a name="clean-up-resources"></a>清理资源

如果还想试验[机密](../secrets/quick-create-python.md)和[密钥](../keys/quick-create-python.md)，可以重复使用本文中创建的 Key Vault。

否则，当完成本文中创建的资源后，请使用以下命令删除资源组及其包含的所有资源：

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>后续步骤

- [Azure 密钥保管库概述](../general/overview.md)
- [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- [Azure Key Vault 最佳做法](../general/best-practices.md)
- [使用 Key Vault 进行身份验证](../general/authentication.md)
