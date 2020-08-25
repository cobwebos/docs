---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Maverics Identity Orchestrator SAML Connector 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Maverics Identity Orchestrator SAML Connector 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518921"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Maverics Identity Orchestrator SAML Connector 的集成

## <a name="introduction"></a>简介

Strata 提供了一种简单方法，可以将本地应用程序与 Azure AD 集成，以进行身份验证和访问控制。

本指南逐步介绍如何配置 Maverics Identity Orchestrator&trade;，以实现以下目标：
* 在登录到旧版本地应用程序期间，将用户从本地标识系统逐步迁移到 Azure AD。
* 将登录请求从旧版 Web 访问管理产品（例如 CA SiteMinder 或 Oracle Access Manager）路由到 Azure AD。
* 在 Azure AD 中对用户进行身份验证后，使用 http 标头或私人会话 cookie 在受保护的本地应用程序中对用户进行身份验证。

Strata 提供了在本地或云中部署的软件，用于跨标识提供者发现、连接和协调，从而为混合和多云企业创建分布式标识管理。

本教程将演示如何迁移当前受旧版 Web 访问管理产品 (CA SiteMinder) 保护的本地 Web 应用程序，从而使用 Azure AD 进行身份验证和访问控制。
1. 安装 Maverics Identity Orchestrator&trade;
2. 在 Azure AD 中注册企业应用程序，然后将其配置为对基于 SAML 的 SSO 使用 Maverics Azure AD SAML Zero Code Connector&trade;。
3. 将 Maverics 与 SiteMinder 和 LDAP 用户存储集成。
4. 设置 Azure Key Vault，并配置 Maverics，将其用作机密管理提供程序。
5. 使用 Maverics 提供对本地 Java Web 应用程序的访问权限，以演示用户迁移和会话抽象。

有关其他安装和配置说明，请访问 https://strata.io/docs

## <a name="prerequisites"></a>先决条件

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
- 已启用 Maverics Identity Orchestrator SAML Connector 单一登录 (SSO) 的订阅。 若要获取 Maverics 软件，请联系 sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>安装 Maverics Identity Orchestrator&trade;

若要开始安装 Maverics Identity Orchestrator，请参阅 https://strata.io/docs 中的安装说明

## <a name="system-requirements"></a>系统要求
### <a name="supported-operating-systems"></a>支持的操作系统
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>依赖项
* systemd

## <a name="installation"></a>安装

1. 获取最新的 Maverics RPM 包。 将包复制到要在其中安装 Maverics 软件的系统。

2. 安装 Maverics 包，并将文件名替换为 `maverics.rpm`。

    `sudo rpm -Uvf maverics.rpm`

3. 安装 Maverics 后，它将作为服务在 `systemd` 下运行。 若要验证服务是否正在运行，请执行以下命令。

    `sudo systemctl status maverics`

默认情况下，Maverics 安装在 `/usr/local/bin` 目录中。

安装 Maverics 后，将在 `/etc/maverics` 目录中创建默认的 `maverics.yaml` 文件。 在编辑配置以添加 `workflows` 和 `connectors` 前，配置文件如下所示：

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>配置选项
### <a name="version"></a>版本
`version` 字段声明所使用的配置文件的版本。 如果未指定，将使用最新的配置版本。

```yaml
version: 0.1
```
### <a name="listen-address"></a>侦听地址
`listenAddress` 声明 Orchestrator 将侦听的地址。 如果地址的主机部分为空，则 Orchestrator 将侦听本地系统所有可用的单播和任意播 IP 地址。 如果地址的端口部分为空，则自动选择端口号。

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls` 字段声明传输层安全对象的映射。 连接器和 Orchestrator 服务器都可以使用 TLS 对象。 有关所有可用的 TLS 选项，请参阅 `transport` 的包文档。

Microsoft Azure 使用基于 SAML 的 SSO 时需要通过 TLS 进行通信，你可以在[此处](https://letsencrypt.org/getting-started/)找到更多证书生成信息。

`maverics` 密钥是为 Orchestrator 服务器保留的。 其他所有密钥均可用，并且可用于将 TLS 对象注入到给定连接器中。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>包含文件

根据以下示例，`connectors` 和 `workflows` 可以在自己的独立配置文件中定义，并且 `maverics.yaml` 可以使用 `includeFiles` 对其进行引用。
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

本教程使用单一 `maverics.yaml` 配置文件。

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>使用 Azure Key Vault 作为机密提供程序

### <a name="secret-management"></a>机密管理

Maverics 可以与各种机密管理解决方案集成，以便加载机密。 当前的集成包括文件、Hashicorp Vault 和 Azure Key Vault。 如果未指定机密管理解决方案，Maverics 将默认从 `maverics.yaml` 中以纯文本格式加载机密。
若要在 `maverics.yaml` 配置文件中将值声明为机密，请用尖括号将机密括起来：

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>文件

若要从文件加载机密，请在文件 `/etc/maverics/maverics.env` 中添加环境变量 `MAVERICS_SECRET_PROVIDER`，以及以下内容：

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

然后重启 maverics 服务：`sudo systemctl restart maverics`

可以用任意数量的 `secrets` 填充 `secrets.yaml` 文件内容。
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

以下步骤演示如何使用 [Azure 门户](https://portal.azure.com)或使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 设置 Azure Key Vault：

1. 使用 Azure 门户或 CLI 命令[登录](https://portal.azure.com)：
    ```shell
    az login
    ```

2. [新建 Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) 或使用 CLI 命令：
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [将机密添加到 Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) 或使用 CLI 命令：
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [将应用程序注册到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)，或使用 CLI 命令：
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [授权应用程序使用机密](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)，或使用 CLI 命令：
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

若要从 Azure KeyVault 加载机密，请按照以下模式使用 azure-credentials.json 文件中的凭据设置 `/etc/maverics/maverics.env` 文件中的环境变量 `MAVERICS_SECRET_PROVIDER`：`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

然后重启 maverics 服务：`sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>在 Azure AD 中为基于 SAML 的 SSO 配置应用程序

1. 在 Azure Active Directory 租户中，导航到 `Enterprise applications`，搜索 `Maverics Identity Orchestrator SAML Connector` 并选中。

2. 在“Maverics Identity Orchestrator SAML Connector”|“属性”页上，将 `User assignment required?` 设置为“否”，确保刚迁移的用户能够正常使用应用程序。

3. 在“Maverics Identity Orchestrator SAML Connector”|“概述”页上，依次选择 `Setup single sign-on` 和 `SAML`。

4. 在“Maverics Identity Orchestrator SAML Connector”|“基于 SAML 的登录”上，编辑基本 SAML 配置。

   ![编辑基本 SAML 配置](common/edit-urls.png)

5. 按照以下模式键入 URL 以设置 `Entity ID`：`https://<SUBDOMAIN>.maverics.org`。 `Entity ID` 在租户的所有应用中必须唯一。 保存此处输入的值，以添加到 Maverics 的配置中。

6. 按照以下模式设置“回复 URL”：`https://<AZURECOMPANY.COM>/<MY_APP>/`。 

7. 按照以下模式设置“登录 URL”：`https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`，然后单击“保存”。

8. 转到“SAML 签名证书”部分，然后单击“复制”按钮以复制“应用联合元数据 URL”，并将其保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Maverics Identity Orchestrator Azure AD SAML Connector 配置

Maverics Identity Orchestrator Azure AD Connector 支持以下内容： 
- OpenID Connect
- SAML Connect 

1. 若要启用基于 SAML 的 SSO，请设置 `authType: saml`。

1. 为 `samlMetadataURL` 创建值：`samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. 现在，在用户使用 Azure 凭据登录后，在应用中定义 Azure 将重定向到的 URL。
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. 从上文中配置的 EntityID 复制值：`samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. 从 Azure AD 将用于发布 SAML 响应的“回复 URL”复制值。
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. 使用 [OpenSSL 工具](https://www.openssl.org/source/)生成 JWT 签名密钥，用于保护 Maverics Identity Orchestrator&trade; 会话信息：

    ```shell 
    openssl rand 64 | base64
    ```
1. 将响应复制到 `jwtSigningKey` 配置属性：`jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>属性和属性映射
属性映射用于定义在预配用户时用户属性从源本地用户目录到 Azure AD 的映射。

属性确定声明中返回到应用程序的用户数据，传递到会话 cookie 的用户数据，或 http 标头变量中传递到应用程序的用户数据。

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>配置 Maverics Identity Orchestrator Azure AD SAML Connector yaml

Maverics Identity Orchestrator Azure AD Connector 配置将如下所示：
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>将用户迁移到 Azure AD

按照此配置，逐步从 Web 访问管理产品（例如 CA SiteMinder、Oracle Access Manager 或 IBM Tivoli）、LDAP 目录或 SQL 数据库中迁移用户。

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>在 Azure AD 中配置用于创建用户的应用程序权限

1. 在 Azure Active Directory 租户中，导航到 `App registrations` 并选择“Maverics Identity Orchestrator SAML Connector”应用程序。

2. 在“Maverics Identity Orchestrator SAML Connector”|“证书和机密”上，选择 `New client secret`，然后选择到期选项。 单击“复制”按钮以复制机密，并将其保存在计算机上。

3. 在“Maverics Identity Orchestrator SAML Connector”|“API 权限”上，选择 `Add permission`，然后在“请求 API 权限”上，依次选择 `Microsoft Graph` 和 `Application permissions`。 在下一个屏幕上，依次选择 `User.ReadWrite.All` 和 `Add permissions`。 完成该操作后会返回到 API 权限，然后选择 `Grant admin consent`。


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>配置 Maverics Identity Orchestrator SAML Connector yaml 以迁移用户

若要启用用户迁移工作流程，请将以下的额外属性添加到配置文件：
1. 设置 Azure Graph URL：`graphURL: https://graph.microsoft.com`
1. 按照以下模式设置 OAuth 标记 URL：`oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. 设置上文中生成的客户端密码：`oauthClientSecret: <CLIENT SECRET>`


最终的 Maverics Identity Orchestrator Azure AD Connector 配置将如下所示：
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>为 SiteMinder 配置 Maverics Zero Code Connector&trade;

可以使用 SiteMinder 连接器将用户迁移到 Azure AD，并使用新创建的 Azure AD 标识和凭据将用户登录到受 SiteMinder 保护的旧版本地应用程序。

在本教程中，已将 SiteMinder 配置为保护使用基于窗体的身份验证和 `SMSESSION` cookie 的旧版应用程序。 若要与通过 http 标头使用身份验证和会话的应用集成，需要将标头仿真配置添加到连接器。

此示例将 `username` 属性映射到 `SM_USER` http 标头：
```yaml
  headers:
    SM_USER: username
```

将 `proxyPass` 设置为代理请求的位置。 通常是受保护应用程序的主机。

重定向用户以进行身份验证时，`loginPage` 应与 SiteMinder 当前使用的登录窗体的 URL 相匹配。

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>为 LDAP 配置 Maverics Zero Code Connector&trade;

当应用程序受 SiteMinder 等 WAM 产品保护时，用户标识和属性通常存储在 LDAP 目录中。

此连接器配置演示如何连接到配置为 SiteMinder 用户存储的 LDAP 目录，以便在迁移工作流期间收集正确的用户配置文件信息，并在 Azure AD 中创建相应的用户。

* `baseDN` 指定目录中执行 LDAP 搜索的位置。

* `url` 是要连接的 LDAP 服务器的地址和端口。

* `serviceAccountUsername` 是用于连接到 LDAP 服务器的用户名，通常表示为绑定 DN，例如 `CN=Directory Manager`。

* `serviceAccountPassword` 是用于连接到 LDAP 服务器的密码。 此值存储在先前配置的 Azure Key Vault 实例中。  

* `userAttributes` 定义一系列要查询的与用户相关的属性。 这些属性后续将映射到相应的 Azure AD 属性。

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>配置迁移工作流

迁移工作流配置确定 Maverics 如何将用户从 SiteMinder/LDAP 迁移到 Azure AD。

此工作流会执行以下操作：
- 使用 SiteMinder 连接器代理 SiteMinder 登录。 通过 SiteMinder 身份验证对用户凭据进行验证，然后将其传递到工作流的后续步骤。
- 从 SiteMinder 用户存储中检索用户配置文件属性。
- 请求 Microsoft Graph API 创建 Azure AD 租户中的用户。

步骤：
1. 为工作流命名，例如 SiteMinder to Azure AD Migration。
2. 指定 `endpoint`，即公开工作流的 HTTP 路径，在响应请求时会触发该工作流的 `actions`。 `endpoint` 通常对应于被代理的应用，例如 `/my_app`。 值必须包含前导和尾随斜杠。
3. 将适当的 `actions` 添加到工作流。
    - 定义 SiteMinder 连接器的 `login` 方法。 连接器值必须与连接器配置中的名称值相匹配。
     - 定义 LDAP 连接器的 `getprofile` 方法。
     - 定义 AzureAD 连接器的 `createuser`。

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>验证迁移工作流

1. 如果 Maverics 服务尚未运行，请执行以下命令将其启动：`sudo systemctl start maverics`

2. 导航到被代理的登录 URL：`http://host.company.com/my_app`。
3. 为用户提供用于登录应用程序且受 SiteMinder 保护的用户凭据。
4. 导航到“主页”>“用户”|“所有用户”，验证用户是否创建在 Azure AD 租户中。  

## <a name="configure-the-session-abstraction-workflow"></a>配置会话抽象工作流

会话抽象工作流将旧版本地 Web 应用程序的身份验证和访问控制迁移到 Azure AD。

假设没有会话，Azure 连接器会使用 `login` 方法将用户重定向到登录 URL。

通过身份验证后，创建的会话令牌会传递到 Maverics，可使用 SiteMinder 连接器的 `emulate` 方法模拟基于 cookie 的会话和/或基于标头的会话，然后使用应用程序所需的其他任何属性修饰请求。

1. 为工作流命名，例如 SiteMinder Session Abstraction。
2. 指定 `endpoint`，它对应于被代理的应用。 值必须包含前导和尾随斜杠，例如 `/my_app/`。
3. 将适当的 `actions` 添加到工作流。
    - 定义 Azure 连接器的 `login` 方法。 `connector` 值必须与连接器配置中的 `name` 值相匹配。
    - 定义 SiteMinder 连接器的 `emulate` 方法。

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>验证会话抽象工作流

1. 导航到被代理的应用程序 URL：`https://<AZURECOMPANY.COM>/<MY_APP>`。 用户将重定向到被代理的登录页面。
2. 输入 Azure AD 用户凭据。
3. 用户应重定向到应用程序，就像直接通过 SiteMinder 进行身份验证一样。
