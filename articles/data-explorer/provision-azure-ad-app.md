---
title: 在 Azure 数据资源管理器中创建 Azure AD 应用程序
description: 了解如何在 Azure 数据资源管理器中创建 Azure AD 应用程序。
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550508"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>在 Azure 数据资源管理器中创建 Azure 活动目录应用程序注册

Azure 活动目录 （Azure AD） 应用程序身份验证用于需要访问 Azure 数据资源管理器且用户不在的情况下的应用程序（如无人参与的服务或计划流）。 如果使用应用程序（如 Web 应用）连接到 Azure 数据资源管理器数据库，则应使用服务主体身份验证进行身份验证。 本文详细介绍了如何创建和注册 Azure AD 服务主体，然后授权它访问 Azure 数据资源管理器数据库。

## <a name="create-azure-ad-application-registration"></a>创建 Azure AD 应用程序注册

Azure AD 应用程序身份验证需要创建应用程序并将其注册到 Azure AD。 在 Azure AD 租户中创建应用程序注册时，将自动创建服务主体。 

1. 登录到[Azure 门户](https://portal.azure.com)并打开`Azure Active Directory`边栏选项卡

    ![从门户菜单中选择 Azure 活动目录](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. 选择**应用注册**边栏选项卡并选择 **"新建注册"**

    ![开始新的应用注册](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. 填充以下信息： 

    * **名称** 
    * **支持的帐户类型**
    * **重定向 URI** > **Web**
        > [!IMPORTANT] 
        > 应用程序类型应为**Web**。 URI 是可选的，在这种情况下留空。
    * 选择 **"注册"**

    ![注册新应用注册](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. 选择 **"概述"** 边栏选项卡并复制**应用程序 ID**。

    > [!NOTE]
    > 您需要应用程序 ID 来授权服务主体访问数据库。

    ![复制应用注册 ID](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. 在 **"证书&机密**"边栏选项卡中，选择 **"新建客户端机密**"

    ![开始创建客户端机密](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > 本文介绍对应用程序的凭据使用客户端机密。  您还可以使用 X509 证书对应用程序进行身份验证。 选择 **"上传证书**"并按照说明上载证书的公共部分。

1. 输入说明、过期并选择 **"添加"**

    ![输入客户端机密参数](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. 复制密钥值。

    > [!NOTE]
    > 离开此页面时，无法访问键值。  您需要密钥才能将客户端凭据配置为数据库。

    ![复制客户端密钥值](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

您的应用程序已创建。 如果只需要访问授权的 Azure 数据资源管理器资源（如下面的编程示例），请跳过下一节。 有关委派权限支持，请参阅[为应用程序注册配置委派权限](#configure-delegated-permissions-for-the-application-registration)。

## <a name="configure-delegated-permissions-for-the-application-registration"></a>为应用程序注册配置委派权限

如果应用程序需要使用调用用户的凭据访问 Azure 数据资源管理器，请为应用程序注册配置委派权限。 例如，如果要构建 Web API 以访问 Azure 数据资源管理器，并且希望使用*调用*API 的用户的凭据进行身份验证。  

1. 在**API 权限**边栏选项卡中，选择 **"添加权限**"。
1. 选择**我的组织使用的 API。** 搜索并选择**Azure 数据资源管理器**。

    ![添加 Azure 数据资源管理器 API 权限](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. 在**委派权限**中，选择**user_impersonation**框并**添加权限**

    ![通过用户模拟选择委派权限](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>授予服务主体对 Azure 数据资源管理器数据库的访问

创建服务主体应用程序注册后，需要授予对 Azure 数据资源管理器数据库的相应服务主体访问权限。 

1. 在[Web UI](https://dataexplorer.azure.com/)中，连接到数据库并打开查询选项卡。

1. 执行以下命令：

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    例如：
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    最后一个参数是查询与数据库关联的角色时以注释显示的字符串。
    
    > [!NOTE]
    > 创建应用程序注册后，可能会有几分钟的延迟，直到 Azure 数据资源管理器可以引用它。 如果在执行命令时收到找不到应用程序的错误，请等待并重试。

有关详细信息，请参阅[安全角色管理和](/azure/kusto/management/security-roles)[引入权限](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)。  

## <a name="using-application-credentials-to-access-a-database"></a>使用应用程序凭据访问数据库

使用应用程序凭据使用[Azure 数据资源管理器客户端库](/azure/kusto/api/netfx/about-kusto-data.md)以编程方式访问数据库。

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > 指定前面创建的应用程序注册（服务主体）的应用程序 ID 和密钥。

有关详细信息，请参阅使用[Azure AD 进行 Azure 数据资源管理器访问进行身份验证](/azure/kusto/management/access-control/how-to-authenticate-with-aad)，并使用[.NET Core Web 应用的 Azure 密钥保管库](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)。

## <a name="troubleshooting"></a>疑难解答

### <a name="invalid-resource-error"></a>无效资源错误

如果应用程序用于验证用户或应用程序以进行 Azure 数据资源管理器访问，则必须为 Azure 数据资源管理器服务应用程序设置委派权限。 声明应用程序可以验证用户或应用程序以进行 Azure 数据资源管理器访问。 如果不这样做，在进行身份验证尝试时，将导致类似于以下内容的错误：

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

您需要按照有关[为 Azure 数据资源管理器服务应用程序设置委派权限的说明](#configure-delegated-permissions-for-the-application-registration)进行操作。

### <a name="enable-user-consent-error"></a>启用用户同意错误

Azure AD 租户管理员可能会制定策略，防止租户用户同意应用程序。 当用户尝试登录到您的应用程序时，这种情况将导致类似于以下内容的错误：

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

您需要与 Azure AD 管理员联系，以便授予租户中的所有用户的同意，或为特定应用程序启用用户同意。

## <a name="next-steps"></a>后续步骤

* 有关支持的连接字符串的列表，请参阅[Kusto 连接字符串](/azure/kusto/api/connection-strings/kusto.md)。
