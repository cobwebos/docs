---
title: 对 Azure Key Vault 进行身份验证
description: 了解如何对 Azure Key Vault 进行身份验证
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6336a0d4d8aa9c781befed0470d9a190af5aa9eb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930853"
---
# <a name="authenticate-to-azure-key-vault"></a>对 Azure Key Vault 进行身份验证

## <a name="overview"></a>概述

Azure Key Vault 是一个机密管理解决方案，你可以使用该解决方案集中存储应用程序机密并控制其分发。 Azure Key Vault 无需在应用程序中存储凭据。 应用程序可以对密钥保管库进行身份验证，以检索所需的凭据。 本文档将介绍对密钥保管库进行身份验证的基础知识。

本文档将帮助你了解密钥保管库身份验证的工作原理。 本文档将介绍身份验证流，向你展示如何授予对密钥保管库的访问权限，并包括一个关于从示例 python 应用程序检索密钥保管库中的存储机密的教程。

本文档将介绍：

* 关键概念
* 安全主体注册
* 了解 Key Vault 身份验证流
* 为服务主体授予对 Key Vault 的访问权限
* 教程 (Python)

## <a name="key-concepts"></a>关键概念

### <a name="azure-active-directory-concepts"></a>Azure Active Directory 概念

* Azure Active Directory (AAD) - Azure Active Directory (Azure AD) 是 Microsoft 推出的基于云的标识和访问管理服务，可帮助员工登录及访问以下位置的资源

* 角色定义 - 角色定义是权限的集合。  AAD 具有标准角色（所有者、参与者或阅读者），这些角色包含对 Azure 资源执行读取、写入和删除等操作的权限级别。 角色也可以是具有特定粒度权限的用户创建的自定义定义。

* 应用程序注册 - 注册 Azure AD 应用程序时，会在 Azure AD 租户中创建两个对象：应用程序对象和服务主体对象。 可以将应用程序对象视为应用程序的全局表示形式（供所有租户使用），将服务主体视为本地表示形式（在特定租户中使用）。

### <a name="security-principal-concepts"></a>安全主体概念

* 安全主体 - 安全主体是一个对象，表示请求访问 Azure 资源的用户、组、服务主体或托管标识。

* 用户 - 在 Azure Active Directory 中具有配置文件的人员。

* 组 - 在 Azure Active Directory 中创建的一组用户。 将某个角色分配到某个组时，该组中的所有用户都拥有该角色。

* 服务主体 - 应用程序或服务用来访问特定 Azure 资源的安全标识。 可将服务主体视为应用程序的用户标识（用户名和密码或证书）。

* 托管标识 - Azure Active Directory 中由 Azure 自动托管的标识。

* 对象 ID（客户端 ID）- Azure AD 生成的唯一标识符，在其初始预配期间与服务主体绑定。

## <a name="security-principal-registration"></a>安全主体注册

1. 管理员在 Azure Active Directory 中注册用户或应用程序（服务主体）。

2. 管理员创建 Azure Key Vault 并配置访问策略 (ACL)。

3. （可选）管理员配置 Azure Key Vault 防火墙。

![IMAGE](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>了解 Key Vault 身份验证流

1. 服务主体通过调用对 AAD 进行身份验证，这可能通过多种方式发生：
    * 用户可以使用用户名和密码登录到 Azure 门户。
    * 应用程序使用客户端 ID，向 AAD 提供客户端机密或客户端证书
    * Azure 资源（如虚拟机）具有分配的 MSI，并联系 IMDS REST 终结点以获取访问令牌。

2. 如果对 AAD 的身份验证成功，将向服务主体授予 OAuth 令牌。
3. 服务主体调用 Key Vault。
4. Azure Key Vault 防火墙决定是否允许调用。
    * 应用场景 1：Key Vault 防火墙已禁用，可从公共 Internet 访问密钥保管库的公共终结点 (URI)。 调用是被允许的。
    * 应用场景 2：调用方是 Azure Key Vault 信任的服务。 如果选择了此选项，某些 Azure 服务可以绕过密钥保管库防火墙。 [Key Vault 信任的服务列表](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * 应用场景 3：调用方按 IP 地址、虚拟网络或服务终结点在 Azure Key Vault 防火墙中列出。
    * 应用场景 4：调用方可以通过配置的专用链接连接访问 Azure Key Vault。
    * 应用场景 5：调用方未获得授权，返回“被禁止”响应。
5. Key Vault 调用 AAD 以验证服务主体的访问令牌。
6. Key Vault 检查服务主体是否具有执行请求操作的足够访问策略权限，在此示例中，该操作是获取机密。
7. Key Vault 向服务主体提供机密。

![IMAGE](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>为服务主体授予对 Key Vault 的访问权限

1. 如果尚未创建服务主体，请创建一个。 [创建服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. 在 Azure Key Vault IAM 设置中向服务主体添加角色分配。 可以添加以下预分配角色：所有者、参与者或读取者。 还可以为服务主体创建自定义角色。 应遵循最小特权主体，并且只提供服务主体所需的最低访问权限。 
3.  配置密钥保管库防火墙。 可以禁用密钥保管库防火墙，并允许从公共 Internet 访问（不太安全，更易于配置）。 还可以限制对特定 IP 范围、服务终结点、虚拟网络或专用终结点的访问（更安全）。
4.  为服务主体添加访问策略，这是服务主体可以在密钥保管库上执行的操作列表。 应使用最小特权主体，并限制服务主体可以执行的操作。 但是，如果你没有提供足够的权限，服务主体的访问将被拒绝。

## <a name="tutorial"></a>教程

在本教程中，你将了解如何设置服务主体以对密钥保管库进行身份验证库并检索机密。 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>第 1 部分：在 Azure 门户创建服务主体

1. 登录到 Azure 门户
1. 搜索“Azure Active Directory”
1. 单击“应用注册”选项卡
1. 单击“+ 新建注册”
1. 创建服务主体的名称
1. 选择“注册”

此时，你有一个已注册的服务主体。 可以通过选择“应用注册”来查看它。 现在，服务主体将分配有客户端 ID GUID，可以将其视为服务主体的“用户名”。 现在我们需要为服务主体创建一个“密码”，你可以使用客户端密码或客户端证书。 请注意，使用客户端密码进行身份验证不安全，应仅用于测试目的。 本教程将演示如何使用客户端证书。

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>第 2 部分：为服务主体创建客户端证书

1. 创建证书

    * 选项 1：使用 [OpenSSL](https://www.openssl.org/) 创建证书（仅出于测试目的，请不要在生产中使用自签名证书）
    * 选项 2：使用密钥保管库创建证书。 [在 Azure Key Vault 中创建证书](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. 下载 PEM/PFX 格式的证书
1. 登录到 Azure 门户，然后导航到 Azure Active Directory
1. 单击“应用注册”
1. 选择在第 1 部分中创建的服务主体。
1. 单击服务主体的“证书和密码”选项卡
1. 使用“上传证书”按钮上传证书

### <a name="part-3-configure-an-azure-key-vault"></a>第 3 部分：配置 Azure Key Vault

1. 创建 Azure Key Vault [链接](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)

2. 配置 Key Vault IAM 权限
    1. 导航到你的密钥保管库
    1. 选择“访问控制(IAM)”选项卡
    1. 单击“添加角色分配”
    1. 从下拉列表中选择“参与者”角色
    1. 输入创建的服务主体的名称或客户端 ID
    1. 单击“查看角色分配”以确认你的服务主体已列出

3. 配置 Key Vault 访问策略权限
    1. 导航到你的密钥保管库
    1. 选择“设置”下的“访问策略”选项卡
    1. 选择“+ 添加访问策略”链接
    1. 在“机密权限”下拉列表中，查看“获取”和“列出”权限。
    1. 按名称或客户端 ID 选择服务主体。
    1. 选择“添加”
    1. 选择“保存”

4. 在密钥保管库中选择密码。
    1. 导航到你的密钥保管库
    1. 单击“设置”下的“密码”选项卡
    1. 单击“+ 生成/导入”
    1. 为密码创建一个名称，在此示例中，我将把密码命名为“test”
    1. 为密码创建一个值，在此示例中，我将把值设置为“password123”

现在，当你从本地计算机运行代码时，可以通过提供客户端 ID 和证书路径获取访问令牌来对密钥保管库进行身份验证。

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>第 4 部分：从应用程序中的 Azure Key Vault 检索密码 (Python)

使用以下代码示例测试应用程序是否可以使用配置的服务主体从密钥保管库检索机密。 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![IMAGE](../media/authentication-3.png)


## <a name="next-steps"></a>后续步骤

1. 了解如何对密钥保管库身份验证错误进行故障排除。 [Key Vault 故障排除指南](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
