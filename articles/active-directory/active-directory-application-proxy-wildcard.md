---
title: "Azure Active Directory 应用程序代理中的通配符应用程序 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 应用程序代理中使用通配符应用程序。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 28e43d48b4fa27202d58ee081a60e2fb5bfe9d99
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory 应用程序代理中的通配符应用程序 

Azure Active Directory (Azure AD) 中配置大量的本地应用程序后，如果其中许多的许多应用程序需要相同的设置，则可能很快就会变得难以管理，并引入不必要的配置错误风险。 使用 [Azure AD 应用程序代理](active-directory-application-proxy-get-started.md)，可以通过通配符应用程序发布功能一次性发布和管理多个应用程序，从而解决此问题。 使用此解决方案可以：

-   简化管理开销
-   减少潜在的配置错误
-   使用户能够安全访问更多资源

本文提供在环境中配置通配符应用程序发布功能所需的信息。


## <a name="create-a-wildcard-application"></a>创建通配符应用程序 

如果一组应用程序采用相同的配置，可以创建一个通配符 (*) 应用程序。 通配符应用程序的潜在候选项为共享以下设置的应用程序：

- 有权访问这些应用程序的用户组
- SSO 方法
- 访问协议（http、https）

如果内部和外部 URL 都采用以下格式，则可以使用通配符发布应用程序：

> http(s)://*.\<domain\>

例如：`http(s)://*.adventure-works.com`。 尽管内部和外部 URL 可以使用不同的域，但最佳做法是使它们保持相同。 发布应用程序时，如果某个 URL 没有通配符，则会出现错误。

如果其他应用程序采用不同的配置设置，则必须将这些例外的应用程序发布为单独的应用程序，以覆盖通配符的默认值设置。 没有通配符的应用程序始终优先于通配符应用程序。 从配置角度讲，前者“只是”普通的应用程序。

创建通配符应用程序的过程基于适用于其他所有应用程序的相同[应用程序发布流](application-proxy-publish-azure-portal.md)。 唯一的区别在于，需在 URL 中包含通配符，有时可以在 SSO 配置中包含通配符。


## <a name="prerequisites"></a>先决条件

### <a name="custom-domains"></a>自定义域

尽管[自定义域](active-directory-application-proxy-custom-domains.md)对于其他所有应用程序是可选的，但它们是通配符应用程序的先决条件。 创建自定义域时需要：

1. 在 Azure 中创建已验证的域 
2. 将 PFX 格式的 SSL 证书上传到应用程序代理。

应考虑使用通配符证书来匹配打算创建的应用程序。 或者，还可以使用仅列出特定应用程序的证书。 在这种情况下，只能通过此通配符应用程序访问证书中列出的应用程序。

出于安全原因，这是一项硬性要求，对于无法将自定义域用于外部 URL 的应用程序，我们不支持通配符。

### <a name="dns-updates"></a>DNS 更新

使用自定义域时，需要创建包含外部 URL（例如 `*.adventure-works.com`）的 CNAME 记录的 DNS 条目，该条目指向应用程序代理终结点的外部 URL。对于通配符应用程序，CNAME 记录需要指向相关的外部 URL：

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

若要确认是否已正确配置 CNAME，可以在某个目标终结点上使用 [nslookup](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup)，例如 `expenses.adventure-works.com`。  响应应包含已提到的别名 (`<Id.tenant>.runtime.msappproxy.net`)。


## <a name="considerations"></a>注意事项


### <a name="accepted-formats"></a>接受的格式

对于通配符应用程序，**内部 URL** 的格式必须为 `http(s)://*.<domain>`。 

![AppId](./media/active-directory-application-proxy-wildcard\22.png)


配置**外部 URL** 时，必须使用以下格式：`https://*.<custom domain>` 

![AppId](./media/active-directory-application-proxy-wildcard\21.png)

通配符的其他位置、多个通配符或其他正则表达式字符串不受支持，并且会导致错误。


### <a name="excluding-applications-from-the-wildcard"></a>从通配符中排除应用程序

可通过以下方式从通配符应用程序中排除某个应用程序

- 将例外的应用程序发布为普通应用程序 
- 通过 DNS 设置仅针对特定应用程序启用通配符  


将应用程序发布为普通应用程序是从通配符中排除应用程序的首选方法。 应在通配符应用程序之前发布排除的应用程序，以确保首先实施例外的项。 最具体的应用程序始终优先 – 发布为 `budgets.finance.adventure-works.com` 的应用程序优先于应用程序 `*.finance.adventure-works.com`，而后者又优先于应用程序 `*.adventure-works.com`。 

还可以通过 DNS 管理将通配符限制为仅对特定的应用程序起作用。 最佳做法是，创建一个包含通配符的 CNAME 条目，并匹配已配置的外部 URL 的格式。 但是，可将特定应用程序的 URL 改为指向通配符。 而不是，将 `hr.adventure-works.com`、`expenses.adventure-works.com` 和 `travel.adventure-works.com individually` 指向 `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`，而不是 `*.adventure-works.com`。 

如果使用此选项，则还需要为值 `AppId.domain` 创建另一个 CNAME 条目，例如，同样指向相同位置的 `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`。 可以在通配符应用程序的应用程序属性页上找到 **AppId**：

![AppId](./media/active-directory-application-proxy-wildcard\01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>设置 MyApps 面板的主页 URL

在 [MyApps 面板](https://myapps.microsoft.com)中，通配符应用程序只是以一个磁贴表示。 此磁贴默认已隐藏。 若要显示该磁贴并让用户首先看到特定的页面：

1. 遵照有关[设置主页 URL](application-proxy-office365-app-launcher.md) 的指导。
2. 在应用程序属性页上将“显示应用程序”设置为“true”。

### <a name="kerberos-constrained-delegation"></a>Kerberos 约束委派

对于[使用 Kerberos 约束委派 (KCD) 作为 SSO 方法](active-directory-application-proxy-sso-using-kcd.md)的应用程序，针对 SSO 方法列出的 SPN 可能也需要通配符。 例如，SPN 可能是：`HTTP/*.adventure-works.com`。 仍需要在后端服务器上配置各个 SPN（例如 `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`）。



## <a name="scenario-1-general-wildcard-application"></a>方案 1：常规通配符应用程序

假设在此方案中，我们想要发布三个不同的应用程序：

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

所有三个应用程序：

- 由所有用户使用
- 使用 *Windows 集成身份验证* 
- 具有相同的属性


可以使用[使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)中所述的步骤发布通配符应用程序。 此方案假设：

- 具有以下 ID 租户：`000aa000-11b1-2ccc-d333-4444eee4444e` 

- 已配置一个名为 `adventure-works.com` 的验证域。

- 已创建将 `*.adventure-works.com` 指向 `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` 的 **CNAME** 条目。

可以遵照[所述的步骤](application-proxy-publish-azure-portal.md)，在租户中创建新的应用程序代理应用程序。 在此示例中，通配符位于以下字段中：

- 内部 URL：

    ![内部 URL](./media/active-directory-application-proxy-wildcard\42.png)


- 外部 URL：

    ![外部 URL](./media/active-directory-application-proxy-wildcard\43.png)

 
- 内部应用程序 SPN： 

    ![SPN 配置](./media/active-directory-application-proxy-wildcard\44.png)


发布通配符应用程序后，可以通过导航到过去经常使用的 URL（例如 `travel.adventure-works.com`）来访问上述三个应用程序。

该配置实现以下结构：

![AppId](./media/active-directory-application-proxy-wildcard\05.png)

| 颜色 | 说明 |
| ---   | ---         |
| 蓝色  | 显式发布并在 Azure 门户中可见的应用程序。 |
| 灰色  | 可通过父应用程序访问的应用程序。 |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>方案 2：带有例外项的常规通配符应用程序

在此方案中，除了三个常规应用程序以外，还有一个仅供财务部门访问的应用程序 `finance.adventure-works.com`。 使用当前应用程序结构时，可通过通配符应用程序和访问财务应用程序，并且所有员工都可访问该应用程序。 若要更改此设置，请将 Finance 配置为权限受到更严格限制的独立应用程序，以便从通配符中排除该应用程序。



需确保有一条 CNAME 记录将 `finance.adventure-works.com` 指向应用程序的应用程序代理页上指定的应用程序特定终结点。 对于此方案，`finance.adventure-works.com` 指向 `https://finance-awcycles.msappproxy.net/`。 

根据[所述的步骤](application-proxy-publish-azure-portal.md)，此方案需要以下设置：


- 在“内部 URL”中，设置 **finance** 而不是通配符。 

    ![内部 URL](./media/active-directory-application-proxy-wildcard\52.png)

- 在“外部 URL”中，设置 **finance** 而不是通配符。 

    ![外部 URL](./media/active-directory-application-proxy-wildcard\53.png)

- 在“内部应用程序 SPN”中，设置 **finance** 而不是通配符。

    ![SPN 配置](./media/active-directory-application-proxy-wildcard\54.png)


此配置实现以下方案：

![方案](./media/active-directory-application-proxy-wildcard\09.png)

由于 `finance.adventure-works.com` 是比 `*.adventure-works.com` 更具体的 URL，因此优先。 导航到 `finance.adventure-works.com` 的用户会获得 Finance Resources 应用程序中指定的体验。 在此情况下，只有财务员工可以访问 `finance.adventure-works.com`。

如果为财务部门发布了多个应用程序，并且使用 `finance.adventure-works.com` 作为验证域，则可以发布另一个通配符应用程序 `*.finance.adventure-works.com`。 因为此应用程序比常规 `*.adventure-works.com` 更具体，因此，此应用程序在用户访问财务域中的应用程序时优先。


## <a name="next-steps"></a>后续步骤

详细信息：

- 有关**自定义域**的信息，请参阅[使用 Azure AD 应用程序代理中的自定义域](active-directory-application-proxy-custom-domains.md)。

- 有关**发布应用程序**的信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)


