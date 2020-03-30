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
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367977"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>使用应用程序代理和 PingAccess 通过基于标头的身份验证进行单一登录

Azure 活动目录 （Azure AD） 应用程序代理已与 PingAccess 合作，以便 Azure AD 客户可以访问更多应用程序。 PingAccess 扩充了[现有的应用程序代理产品/服务](application-proxy.md)，即，增加了对使用标头进行身份验证的应用程序的单一登录访问功能。

## <a name="whats-pingaccess-for-azure-ad"></a>什么是 Azure AD 的 Ping Access？

使用 Azure AD 的 PingAccess，您可以为用户提供对使用标头进行身份验证的应用程序的访问权限和单一登录 （SSO）。 应用程序代理将这些应用程序视为与其他任何应用程序一样，它使用 Azure AD 对访问进行身份验证，然后通过连接器服务传递流量。 PingAccess 位于应用程序前面，并将 Azure AD 的访问令牌转换为标头。 然后，应用程序以它可以读取的格式接收身份验证。

用户登录后使用企业应用程序时，感觉不到任何差异。 他们仍可在任何位置的任何设备上工作。 应用程序代理连接器将远程流量定向到所有应用，而不考虑其身份验证类型，因此它们仍会自动平衡负载。

## <a name="how-do-i-get-access"></a>如何获取访问权限？

由于此方案来自 Azure 活动目录和 PingAccess 之间的合作关系，因此需要两个服务的许可证。 但是，Azure Active Directory Premium 订阅包含一个 PingAccess 基本许可证，可涵盖多达 20 个应用程序。 如果需要发布 20 个以上基于标头的应用程序，可以再从 PingAccess 购买一个许可证。

有关详细信息，请参阅[Azure 活动目录版本](../fundamentals/active-directory-whatis.md)。

## <a name="publish-your-application-in-azure"></a>在 Azure 中发布应用程序

本文供人们首次使用此方案发布应用程序。 除了详细说明发布步骤外，它还指导您开始使用应用程序代理和 PingAccess。 如果已配置了两个服务，但希望对发布步骤进行刷新，请跳到["使用应用程序代理将应用程序添加到 Azure AD"](#add-your-application-to-azure-ad-with-application-proxy)部分。

> [!NOTE]
> 由于本方案是 Azure AD 与 PingAccess 之间的合作成果，某些说明已在 Ping Identity 站点上提供。

### <a name="install-an-application-proxy-connector"></a>安装应用程序代理连接器

如果已启用应用程序代理并安装连接器，则可以跳过此部分，然后转到[使用应用程序代理将应用程序添加到 Azure AD。](#add-your-application-to-azure-ad-with-application-proxy)

应用程序代理连接器是 Windows Server 服务，可将流量从远程员工定向到已发布的应用程序。 有关更详细的安装说明，请参阅[教程：在 Azure 活动目录中添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。

1. 以应用程序管理员身份登录到[Azure 活动目录门户](https://aad.portal.azure.com/)。 此时会显示“Azure Active Directory 管理中心”页。****
1. 选择**Azure 活动目录** > **应用程序代理** > **下载连接器服务**。 将显示**应用程序代理连接器下载**页面。

   ![应用程序代理连接器下载](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 按照安装说明进行操作。

下载连接器应自动为目录启用应用程序代理，但如果没有，则可以选择**启用应用程序代理**。

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>使用应用程序代理将应用程序添加到 Azure AD

需要在 Azure 门户中执行两项操作。 首先，需要使用应用程序代理发布应用程序。 然后，您需要收集有关应用程序的某些信息，您可以在 PingAccess 步骤中使用这些信息。

#### <a name="publish-your-application"></a>发布应用程序

您首先必须发布应用程序。 此操作涉及：

- 将本地应用程序添加到 Azure AD
- 分配用户以测试应用程序并选择基于标头的 SSO
- 设置应用程序的重定向 URL
- 授予用户和其他应用程序使用本地应用程序的权限

要发布您自己的本地应用程序，请执行：

1. 如果没有在最后一节中，请以应用程序管理员身份登录到 Azure[活动目录门户](https://aad.portal.azure.com/)。
1. 选择**企业应用程序** > **新应用程序** > **添加本地应用程序**。 将显示 **"添加您自己的本地应用程序**"页。

   ![添加您自己的本地应用程序](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 填写所需字段，包含有关新应用程序的信息。 使用下面的指南进行设置。

   > [!NOTE]
   > 有关此步骤的更详细演练，请参阅[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。

   1. **内部 URL**：通常，当您在公司网络上时，您会提供将您带到应用登录页面的 URL。 对于此方案，连接器需要将 PingAccess 代理视为应用程序的首页。 使用此格式：`https://<host name of your PingAccess server>:<port>`。 默认情况下端口为 3000，但可以在 PingAccess 中对其进行配置。

      > [!WARNING]
      > 对于这种类型的单登录，内部 URL 必须使用`https`并且不能使用`http`。

   1. **预身份验证方法**：选择**Azure 活动目录**。
   1. **在标题中翻译 URL：** 选择**否**。

   > [!NOTE]
   > 如果这是第一个应用程序，则从使用端口 3000 开始，如果更改了 PingAccess 配置，则返回以更新此设置。 对于后续应用程序，端口将需要与在 PingAccess 中配置的侦听器匹配。 详细了解 [PingAccess 中的侦听器](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)。

1. 选择“添加”****。 将显示新应用程序的概述页。

现在分配用户进行应用程序测试，并选择基于标头的单一登录：

1. 从应用程序侧边栏中，选择 **"用户"和"组** > **添加用户** > **和组"（\<数字>已选择）。** 将显示一个用户和组列表供您选择。

   ![显示用户和组的列表](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 选择用户进行应用程序测试，然后**选择**。 确保此测试帐户有权访问本地应用程序。
1. 选择“分配”。****
1. 从应用程序侧边栏中，选择**基于****单一登录标头** > 。

   > [!TIP]
   > 如果这是第一次使用基于标头的单一登录，则需安装 PingAccess。 为了确保 Azure 订阅与 PingAccess 安装自动关联，请使用此单一登录页上的链接下载 PingAccess。 可以现在就打开下载站点，也可以稍后返回到此页面。

   ![显示基于头的登录屏幕和 PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. 选择“保存”。****

然后，请确保您的重定向 URL 已设置为外部 URL：

1. 从**Azure 活动目录管理中心**侧边栏中，选择**Azure 活动目录** > **应用注册**。 将显示应用程序列表。
1. 选择自己的应用程序。
1. 选择**重定向 URI**旁边的链接，显示为 Web 和公共客户端设置的重定向 URI 数。 **\<将显示应用程序名称> - 身份验证**页。
1. 检查之前分配给应用程序的外部 URL 是否位于**重定向 URI**列表中。 如果不是，请立即添加外部 URL，使用 Web**的重定向**URI 类型，然后选择 **"保存**"。

最后，设置本地应用程序，以便用户具有读取访问权限，其他应用程序具有读/写访问权限：

1. 从应用程序**注册**侧边栏中，选择 API**权限** > **添加** > **微软 API** > **微软图形**的权限 。 将显示**Microsoft 图形**的请求**API 权限**页，其中包含 Windows Azure 活动目录的 API。

   ![显示请求 API 权限页](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. 选择**委派权限** > **用户** > **。**
1. 选择**应用程序权限** > **应用程序** > **.读取写入。所有**。
1. 选择“添加权限”****。
1. 在**API 权限**页中，选择 **"授予\<管理员同意>目录名称**。

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步骤的信息

您需要收集这三条信息（所有 GUID）才能使用 PingAccess 设置应用程序：

| Azure AD 字段的名称 | PingAccess 字段的名称 | 数据格式 |
| --- | --- | --- |
| **应用程序(客户端) ID** | **客户端 ID** | GUID |
| **目录(租户) ID** | **发行** | GUID |
| `PingAccess key` | **客户端机密** | 随机字符串 |

要收集此信息，

1. 从**Azure 活动目录管理中心**侧边栏中，选择**Azure 活动目录** > **应用注册**。 将显示应用程序列表。
1. 选择自己的应用程序。 将显示应用程序**的应用注册**页。

   ![应用程序的注册概述](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. 在**应用程序（客户端）ID**值旁边，选择 **"复制到剪贴板**"图标，然后复制并保存它。 稍后将此值指定为 PingAccess 的客户端 ID。
1. 接下来目录 **（租户）ID**值，也选择 **"复制到剪贴板**"，然后复制并保存它。 稍后将此值指定为 PingAccess 的颁发者。
1. 从应用程序注册为应用程序**注册**侧边栏，选择**证书和机密** > **新客户端机密**。 将显示 **"添加客户端机密**"页。

   ![显示"添加客户端机密页"](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. 在**描述**`PingAccess key`中，键入 。
1. 在 **"过期"** 下，选择如何设置 PingAccess 键：**在 1 年内**，在 2**年**，或**永远不会**。
1. 选择“添加”****。 PingAccess 键显示在客户端机密表中，其中随机字符串在**VALUE**字段中自动填充。
1. 在 PingAccess 键的**VALUE**字段旁边，选择 **"复制到剪贴板**"图标，然后复制并保存它。 稍后将此值指定为 PingAccess 的客户端机密。

**更新字段`acceptMappedClaims`：**

1. 以应用程序管理员身份登录到[Azure 活动目录门户](https://aad.portal.azure.com/)。
1. 选择**Azure 活动目录** > **应用注册**。 将显示应用程序列表。
1. 选择自己的应用程序。
1. 从应用程序**注册**页面的边栏中，选择 **"清单**"。 将显示应用程序注册的清单 JSON 代码。
1. 搜索字段`acceptMappedClaims`，并将值更改为`True`。
1. 选择“保存”。****

### <a name="use-of-optional-claims-optional"></a>使用可选声明（可选）

可选声明允许您添加每个用户和租户具有的标准但未包含的声明。 您可以通过修改应用程序清单为应用程序配置可选声明。 有关详细信息，请参阅[了解 Azure AD 应用程序清单一文](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

将电子邮件地址包含在 PingAccess 将使用access_token中的示例：
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

### <a name="use-of-claims-mapping-policy-optional"></a>使用声明映射策略（可选）

对 AzureAD 中不存在的属性[的声明映射策略（预览](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties)）。 声明映射允许您通过添加由 ADFS 或用户对象支持的其他自定义声明将旧的 prem 应用迁移到云

要使应用程序使用自定义声明并包含其他字段，请确保还[创建了自定义声明映射策略并将其分配给应用程序](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

> [!NOTE]
> 若要使用自定义声明，还必须定义自定义策略并将其分配给应用程序。 此策略应包括所有必需的自定义属性。
>
> 您可以通过 PowerShell 或 Microsoft 图形执行策略定义和分配。 如果您在 PowerShell 中执行，则可能需要首先使用`New-AzureADPolicy`，然后将其分配给使用`Add-AzureADServicePrincipalPolicy`的应用程序。 有关详细信息，请参阅[声明映射策略分配](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

示例：
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>启用 PingAccess 使用自定义声明

启用 PingAccess 使用自定义声明是可选的，但如果希望应用程序使用其他声明，则需要使用。

在以下步骤中配置 PingAccess 时，您将创建的 Web 会话（设置->访问>Web 会话）必须取消选择 **"请求配置文件**"，**并刷新用户属性**设置为 **"否"**

## <a name="download-pingaccess-and-configure-your-application"></a>下载 PingAccess 并配置应用程序

完成 Azure Active Directory 的所有设置步骤后，可以开始配置 PingAccess。

此方案的 PingAccess 部分的详细步骤将继续在 Ping 标识文档中。 按照为 Azure [AD 配置 PingAccess 中的说明进行操作，以保护在](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)Ping 标识网站上使用 Microsoft Azure AD 应用程序代理发布的应用程序。

这些步骤可帮助您安装 PingAccess 并设置 PingAccess 帐户（如果您还没有帐户）。 然后，要创建 Azure AD OpenID 连接 （OIDC） 连接，请设置一个令牌提供程序，该提供程序具有从 Azure AD 门户复制的**目录（租户）ID**值。 接下来，要在 PingAccess 上创建 Web 会话，请使用**应用程序（客户端）ID**和`PingAccess key`值。 接下来，可以设置标识映射并创建虚拟主机、站点和应用程序。

### <a name="test-your-application"></a>测试应用程序

完成所有这些步骤后，应用程序应启动并运行。 要测试它，请打开浏览器并导航到在 Azure 中发布应用程序时创建的外部 URL。 使用分配给应用程序的测试帐户登录。

## <a name="next-steps"></a>后续步骤

- [为 Azure AD 配置 PingAccess 以保护使用 Microsoft Azure AD 应用程序代理发布的应用程序](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)
