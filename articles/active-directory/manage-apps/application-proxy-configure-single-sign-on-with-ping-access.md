---
title: 使用 PingAccess for Azure AD 应用程序代理进行基于标头的身份验证 | Microsoft Docs
description: 使用 PingAccess 和应用代理发布应用程序，以支持基于标头的身份验证。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c43498a7829a43fad331841aca045f52ae680be
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481468"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>使用应用程序代理和 PingAccess 通过基于标头的身份验证进行单一登录

Azure Active Directory (Azure AD) 应用程序代理已经与 PingAccess 合作，以便你的 Azure AD 客户可以访问的应用程序的详细信息。 PingAccess 扩充了[现有的应用程序代理产品/服务](application-proxy.md)，即，增加了对使用标头进行身份验证的应用程序的单一登录访问功能。

## <a name="whats-pingaccess-for-azure-ad"></a>什么是 PingAccess for Azure AD？

使用 Azure AD 的 PingAccess，您可以让用户访问和单一登录 (SSO) 访问应用程序使用标头进行身份验证。 应用程序代理将这些应用程序视为与其他任何应用程序一样，它使用 Azure AD 对访问进行身份验证，然后通过连接器服务传递流量。 PingAccess 位于应用程序的前面，并从 Azure AD 访问令牌转换为标头。 然后，应用程序可以读取的格式接收身份验证。

用户登录后使用企业应用程序时，感觉不到任何差异。 他们仍可在任何位置的任何设备上工作。 应用程序代理连接器远程将流量定向到所有应用，而不考虑其身份验证类型，因此它们仍将自动平衡负载。

## <a name="how-do-i-get-access"></a>如何获取访问权限？

由于此方案中来自 Azure Active Directory 与 PingAccess 之间的合作关系，您将需要许可证，这两个服务。 但是，Azure Active Directory Premium 订阅包含一个 PingAccess 基本许可证，可涵盖多达 20 个应用程序。 如果需要发布 20 个以上基于标头的应用程序，可以再从 PingAccess 购买一个许可证。

有关详细信息，请参阅 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="publish-your-application-in-azure"></a>在 Azure 中发布应用程序

本文适用于发布这种情况在第一次应用程序的人员。 除详细介绍的发布步骤，它将引导您在开始使用应用程序代理和 PingAccess。 如果你已配置这两个服务，但想要重温发布步骤，请跳到[添加到 Azure AD 应用程序使用应用程序代理](#add-your-application-to-azure-ad-with-application-proxy)部分。

> [!NOTE]
> 由于本方案是 Azure AD 与 PingAccess 之间的合作成果，某些说明已在 Ping Identity 站点上提供。

### <a name="install-an-application-proxy-connector"></a>安装应用程序代理连接器

如果已启用应用程序代理已启用并已安装连接器，则可以跳过此部分并转到[添加到 Azure AD 应用程序使用应用程序代理](#add-your-application-to-azure-ad-with-application-proxy)。

应用程序代理连接器是一个 Windows Server 服务，将定向到发布的应用程序的远程员工的流量。 有关更详细安装说明，请参阅[教程：在 Azure Active Directory 中添加用于通过应用程序代理远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)作为应用程序管理员。 此时会显示“Azure Active Directory 管理中心”页。 
2. 选择**Azure Active Directory** > **应用程序代理** > **下载连接器服务**。 **应用程序代理连接器下载**页将出现。

   ![下载应用程序代理连接器](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. 遵照安装说明操作。

下载连接器应自动启用应用程序代理为你的目录，但如果不是，你可以选择**启用应用程序代理**。

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>添加到 Azure AD 应用程序使用应用程序代理

需要在 Azure 门户中执行两项操作。 首先，需要使用应用程序代理发布应用程序。 然后，您需要收集一些有关可以在执行 PingAccess 步骤期间使用的应用程序信息。

#### <a name="publish-your-application"></a>发布应用程序

首先，将需要发布应用程序。 此操作的过程包括：

- 添加到 Azure AD 的本地应用程序
- 将用户分配用于测试应用程序和选择基于标头的 SSO
- 设置应用程序的重定向 URL
- 授予用户和其他应用程序以使用你的本地应用程序的权限

若要发布的本地应用程序：

1. 如果未在上一部分中，登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)作为应用程序管理员。
1. 选择**企业应用程序** > **新的应用程序** > **的本地应用程序**。 **添加自己的本地应用程序**页将出现。

   ![添加你自己的本地应用程序](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 填写必填字段的新应用程序有关的信息。 设置使用下面的指南。

   > [!NOTE]
   > 此步骤的更详细演练，请参阅[添加到 Azure AD 的本地应用](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。

   1. **内部 URL**：通常您提供您在公司网络上时转到应用程序的登录页的 URL。 对于此方案，连接器需要将 PingAccess 代理视为应用程序的首页。 使用此格式：`https://<host name of your PingAccess server>:<port>`。 默认情况下端口为 3000，但可以在 PingAccess 中对其进行配置。

      > [!WARNING]
      > 对于此类型的单一登录，必须使用内部 URL`https`并且不能使用`http`。

   1. **预身份验证方法**：选择**Azure Active Directory**。
   1. **转换标头中的 URL**：选择**否**。

   > [!NOTE]
   > 如果这是第一个应用程序，则从使用端口 3000 开始，如果更改了 PingAccess 配置，则返回以更新此设置。 对于后续应用程序，需要匹配已在 PingAccess 中配置的侦听器端口。 详细了解 [PingAccess 中的侦听器](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)。

1. 选择 **添加** 。 新的应用程序的概述页会显示。

现在将应用程序测试用户分配，并选择基于标头的单一登录：

1. 从应用程序边栏中，选择**用户和组** > **添加用户** > **用户和组 (\<数 > 所选)** . 可供选择，将显示用户和组的列表。

   ![显示用户和组的列表](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 选择用户的应用程序测试，并选择**选择**。 确保此测试帐户有权访问本地应用程序。
1. 选择“分配”。 
1. 从应用程序边栏中，选择**单一登录** > **基于标头的**。

   > [!TIP]
   > 如果这是第一次使用基于标头的单一登录，则需安装 PingAccess。 为了确保 Azure 订阅与 PingAccess 安装自动关联，请使用此单一登录页上的链接下载 PingAccess。 可以现在就打开下载站点，也可以稍后返回到此页面。

   ![显示基于标头的登录屏幕上和 PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. 选择“保存”。 

然后确保重定向 URL 设置为外部 URL:

1. 从**Azure Active Directory 管理中心**侧栏中，选择**Azure Active Directory** > **应用注册**。 将显示应用程序的列表。
1. 选择自己的应用程序。
1. 下一步选择的链接**重定向 Uri**，显示重定向 Uri 的 web 和公共客户端设置的数目。 **\<应用程序名称 >-身份验证**页将出现。
1. 检查是否为分配给你的应用程序之前的外部 URL**重定向 Uri**列表。 如果不是，现在将添加的外部 URL，使用的重定向 URI 类型为**Web**，然后选择**保存**。

最后，设置你的本地应用程序，以便用户可以读取访问和其他应用程序具有读/写访问权限：

1. 从**应用注册**侧栏以应用程序中，选择**API 权限** > **添加权限** >  **Microsoft Api** > **Microsoft Graph**。 **请求 API 的权限**页上的**Microsoft Graph**出现，其中包含 Api 的 Windows Azure Active Directory。

   ![显示请求 API 权限页](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. 选择**委托的权限** > **用户** > **User.Read**。
1. 选择**应用程序权限** > **应用程序** > **Application.ReadWrite.All**。
1. 选择“添加权限”  。
1. 在中**API 的权限**页上，选择**授予管理员许可\<你的目录名称 >** 。

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步骤的信息

您需要收集这三个条信息 (所有 Guid) 以与 PingAccess 将应用程序设置：

| Azure AD 域的名称 | PingAccess 字段的名称 | 数据格式 |
| --- | --- | --- |
| **应用程序 （客户端） ID** | **客户端 ID** | GUID |
| **目录 （租户） ID** | **颁发者** | GUID |
| `PingAccess key` | **客户端机密** | 随机字符串 |

若要收集此信息：

1. 从**Azure Active Directory 管理中心**侧栏中，选择**Azure Active Directory** > **应用注册**。 将显示应用程序的列表。
1. 选择自己的应用程序。 **应用注册**页上将显示你的应用程序。

   ![注册应用程序的概述](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. 下一步**应用程序 （客户端） ID**值，请选择**复制到剪贴板**图标，然后复制并保存它。 指定此值更高版本为 PingAccess 的客户端 id。
1. 下一步**目录 （租户） ID**值，还选择**复制到剪贴板**，然后复制并将其保存。 为 PingAccess 的颁发者的更高版本指定此值。
1. 从的边栏**应用注册**对于应用程序中，选择**证书和机密** > **新客户端机密**。 **将客户端机密添加**页将出现。

   ![显示添加了一个客户端机密页](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. 在中**描述**，类型`PingAccess key`。
1. 下**Expires**，选择如何设置 PingAccess 项：**1 年内**， **2 年内**，或**从不**。
1. 选择 **添加** 。 PingAccess 密钥显示在客户端机密的表使用的随机字符串中的，会自动**值**字段。
1. PingAccess 密钥旁边**值**字段中，选择**复制到剪贴板**图标，然后复制并保存它。 为 PingAccess 的客户端机密的更高版本指定此值。

### <a name="update-graphapi-to-send-custom-fields-optional"></a>更新 GraphAPI 以发送自定义字段 （可选）

如果需要发送其他令牌中的自定义声明由 PingAccess 使用 access_token，设置`acceptMappedClaims`应用程序的字段`True`。 可以使用图形资源管理器或 Azure AD 门户的应用程序清单，以进行此更改。

**此示例使用图形资源管理器：**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**此示例使用[Azure Active Directory 门户](https://aad.portal.azure.com/)更新`acceptMappedClaims`字段：**

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)作为应用程序管理员。
1. 选择“Azure Active Directory” > “应用注册”。   将显示应用程序的列表。
1. 选择自己的应用程序。
1. 从的边栏**应用注册**选择应用程序页**清单**。 将显示应用程序的注册的清单 JSON 代码。
1. 搜索`acceptMappedClaims`字段，并将值更改为`True`。
1. 选择“保存”。 

### <a name="use-of-optional-claims-optional"></a>使用可选声明 （可选）

可选声明，可添加的每个用户和租户有 standard-but-not-included-by-default 声明。 可以通过修改应用程序清单来配置应用程序的可选声明。 有关详细信息，请参阅[了解 Azure AD 应用程序清单文章](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

若要将电子邮件地址包含到将使用 PingAccess 的 access_token 的示例：
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>使用声明映射策略 （可选）

[声明映射策略 （预览）](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/)的 azure Ad 中不存在特性。 声明映射，可通过添加其他自定义声明支持 ADFS 或用户对象的旧的本地应用迁移到云

若要使应用程序使用的自定义声明并包括其他字段，请确保你已还[创建自定义声明映射策略并将其分配到应用程序](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

> [!NOTE]
> 若要使用自定义声明，还必须定义自定义策略并将其分配给应用程序。 此策略应包括所有必需的自定义属性。
>
> 你可以执行策略定义和分配通过 PowerShell、 Azure AD Graph 资源管理器或 Microsoft Graph。 如果要在 PowerShell 中执行它们，您可能需要首先使用`New-AzureADPolicy`然后将其分配到应用程序使用`Add-AzureADServicePrincipalPolicy`。 有关详细信息，请参阅[声明映射策略分配](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

示例：
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>启用要使用自定义声明的 PingAccess

启用 PingAccess 以使用自定义声明是可选的但需要如果希望应用程序以使用其他声明。

在将在下一步配置 PingAccess 时，Web 会话您将创建 (设置-> 访问-> Web 会话) 必须具有**请求的配置文件**取消选择和**刷新用户属性**设置为**否**

## <a name="download-pingaccess-and-configure-your-application"></a>下载 PingAccess 并配置你的应用程序

完成 Azure Active Directory 的所有设置步骤后，可以开始配置 PingAccess。

PingAccess 属于此方案的详细的步骤继续 Ping Identity 文档中。 按照中的说明[Azure AD 保护使用 Microsoft Azure AD 应用程序代理发布的应用程序的配置 PingAccess](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) Ping Identity web 站点上。

这些步骤帮助你安装 PingAccess 和设置 PingAccess 帐户 （如果还没有一个）。 然后，若要创建的 Azure AD OpenID Connect (OIDC) 连接，设置使用的令牌提供程序**目录 （租户） ID**从 Azure AD 门户中复制的值。 接下来，若要在 PingAccess 中创建 web 会话，则使用**应用程序 （客户端） ID**和`PingAccess key`值。 接下来，可以设置标识映射并创建虚拟主机、站点和应用程序。

### <a name="test-your-application"></a>测试应用程序

完成所有这些步骤后，你的应用程序应启动并运行。 若要对其进行测试，打开浏览器并导航到在 Azure 中发布应用程序时创建的外部 URL。 使用分配给应用程序的测试帐户登录。

## <a name="next-steps"></a>后续步骤

- [配置 PingAccess for Azure AD 保护使用 Microsoft Azure AD 应用程序代理发布的应用程序](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)
