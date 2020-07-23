---
title: Azure Active Directory 条件访问中的位置条件
description: 了解如何使用位置条件基于用户的网络位置来控制对云应用的访问。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 091555bac780f3abc7089d6214d0f9a8cfbf98cd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518430"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>在条件访问策略中使用位置条件 

如[概述文章](overview.md)中所述，条件访问策略归根到底就是一个 if-then 语句，该语句用于组合信号、做出决策和强制实施组织策略。 可以引入到决策过程中的信号之一是网络位置。

![概念性条件信号加上要实施的决策](./media/location-condition/conditional-access-signal-decision-enforcement.png)

组织可以将此网络位置用于常见任务，例如： 

- 要求用户在企业网络外部访问服务时执行多重身份验证。
- 阻止特定国家或地区的用户访问服务。

网络位置由客户端提供给 Azure Active Directory 的公共 IP 地址确定。 默认情况下，条件访问策略应用于所有 IPv4 和 IPv6 地址。 

> [!TIP]
> 只有 **[命名位置（预览版）](#preview-features)** 接口中支持 IPv6 范围。 

## <a name="named-locations"></a>命名位置

位置在 Azure 门户中的“Azure Active Directory” > “安全性” > “条件访问” > “命名位置”下指定。 这些命名网络位置可能包括组织的总部网络范围、VPN 网络范围或你希望阻止的范围等位置。 

![Azure 门户中的命名位置](./media/location-condition/new-named-location.png)

若要配置位置，至少需要提供**名称**和 IP 范围。 

可配置的已命名位置数受限于 Azure AD 中相关对象的大小。 可以根据以下限制来配置位置：

- 一个命名位置最多可以有 1200 个 IPv4 范围。
- 最多可有 90 个命名位置，其中每个都分配有一个 IP 范围。

> [!TIP]
> 只有 **[命名位置（预览版）](#preview-features)** 接口中支持 IPv6 范围。 

### <a name="trusted-locations"></a>受信任位置

创建网络位置时，管理员可以选择将某个位置标记为受信任位置。 

![Azure 门户中的受信任位置](./media/location-condition/new-trusted-location.png)

在条件访问策略中可以考虑此选项，例如，你可以要求从受信任的网络位置注册多重身份验证。 它还会影响到 Azure AD Identity Protection 的风险计算，降低用户从标记为受信任的位置进入的登录风险。

### <a name="countries-and-regions"></a>国家/地区和区域

某些组织可能会选择将整个国家/地区或区域 IP 边界定义为条件访问策略的命名位置。 如果组织知道有效用户永远不会来自某个位置（例如朝鲜），则他们可以根据位置阻止不必要的流量。 IP 地址到国家/地区的这些映射会定期更新。 

> [!NOTE]
> 国家/地区不包含 IPv6 地址范围，仅包含已知的 IPv4 地址范围，不能将其标记为“可信”。

![在 Azure 门户中创建新的基于国家/地区或区域的位置](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>包含未知区域

某些 IP 地址未映射到特定的国家/地区或区域。 若要捕获这些 IP 位置，请在定义位置时选中“包含未知区域”复选框。 使用此选项可以选择这些 IP 地址是否应包含在命名位置中。 如果使用命名位置的策略需要应用到未知位置，则使用此设置。

### <a name="configure-mfa-trusted-ips"></a>配置 MFA 受信任的 IP

还可以在[多重身份验证服务设置](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)中配置 IP 地址范围，用于表示组织的本地 Intranet。 使用此功能最多可以配置 50 个 IP 地址范围。 IP 地址范围采用 CIDR 格式。 有关详细信息，请参阅[受信任的 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)。  

如果已配置受信任的 IP，这些 IP 将作为“MFA 受信任的 IP”显示在位置条件的位置列表中。

### <a name="skipping-multi-factor-authentication"></a>跳过多重身份验证

在多重身份验证服务设置页中，可以通过选择“跳过多重身份验证以适用于我的 Intranet 上的联合用户发出的请求”，来标识企业 Intranet 用户。 此设置指示 AD FS 颁发的内部企业网络声明应受信任，并且应该用于将用户标识为位于企业网络中。 有关详细信息，请参阅[使用条件访问启用受信任的 IP 功能](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)。

选中此选项之后，“MFA 受信任的 IP”（包括命名位置）将应用到已选择此选项的所有策略。

对于会话生存期较长的移动和桌面应用程序，将定期重新评估条件访问。 默认设置是一小时评估一次。 如果只在初始身份验证时才颁发内部企业网络声明，则 Azure AD 可能没有受信任的 IP 范围列表。 在这种情况下，更难以确定用户是否仍在企业网络中：

1. 检查用户的 IP 地址是否在某个受信任的 IP 范围内。
1. 检查用户 IP 地址的前三个八位字节是否匹配初始身份验证 IP 地址的前三个八位字节。 当内部企业网络声明最初是初次颁发且用户位置已经过验证时，IP 地址将与初始身份验证进行比较。

如果这两个步骤均失败，则将用户视为不再位于受信任的 IP 中。

## <a name="preview-features"></a>预览功能

除了正式发布的命名位置功能之外，还有一个命名位置（预览版）。 通过使用当前的命名位置边栏选项卡顶部的横幅，可以访问命名位置预览版。

![试用命名位置预览版](./media/location-condition/preview-features.png)

使用命名位置预览版，你可以：

- 配置最多 195 个命名位置
- 为每个命名位置配置最多 2000 个 IP 范围
- 将 IPv6 地址与 IPv4 地址一起配置

我们还添加了一些额外的检查，帮助你减少错误配置的更改。

- 无法再配置专用 IP 范围
- 范围中可以包含的 IP 地址数有限制。 配置 IP 范围时只允许使用大于 /8 的 CIDR 掩码。

使用预览版，现在有两个创建选项： 

- **国家/地区位置**
- **IP 范围位置**

> [!NOTE]
> 国家/地区不包含 IPv6 地址范围，仅包含已知的 IPv4 地址范围，不能将其标记为“可信”。

![命名位置预览版界面](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>策略中的位置条件

配置位置条件时，可以选择区分：

- 任何位置
- 所有受信任的位置
- 选定的位置

### <a name="any-location"></a>任何位置

默认情况下，选择“任何位置”会将策略应用到所有 IP 地址，即 Internet 上的所有地址。 此设置不限于已配置为命名位置的 IP 地址。 选择“任何位置”时，仍可从策略中排除特定位置。 例如，可将策略应用到除可信位置以外的所有位置，以将作用域设置为除企业网络以外的所有位置。

### <a name="all-trusted-locations"></a>所有受信任的位置

此选项将应用到：

- 已标记为可信位置的所有位置
- **MFA 受信任的 IP**（如果已配置）

### <a name="selected-locations"></a>选定的位置

使用此选项可以选择一个或多个命名位置。 对于要应用此设置的策略，用户需要从任一选定位置建立连接。 单击“选择”时，将会打开显示命名网络列表的命名网络选择控件。 此列表还显示网络位置是否已标记为可信。 名为“MFA 受信任的 IP”的命名位置用于包含可在多重身份验证服务设置页中配置的 IP 设置。

## <a name="ipv6-traffic"></a>IPv6 流量

默认情况下，条件访问策略将应用于所有 IPv6 流量。 使用[命名位置预览](#preview-features)，可以从条件访问策略中排除特定的 IPv6 地址范围。 当不想针对特定 IPv6 范围强制执行策略时，此选项非常有用。 例如，如果不想强制执行针对企业网络使用的策略，且企业网络托管在公共 IPv6 范围内。  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>我的租户什么时候会有 IPv6 流量？

Azure Active Directory (Azure AD) 当前不支持使用 IPv6 的直接网络连接。 但在某些情况下，身份验证流量是通过其他服务代理的。 在这些情况下，将在策略评估期间使用 IPv6 地址。

通过代理发送到 Azure AD 的大多数 IPv6 流量来自 Microsoft Exchange Online。 如果可行，Exchange 会首选 IPv6 连接。 **因此，如果你有针对特定 IPv4 范围配置的 Exchange 条件访问策略，需要确保你还添加了组织的 IPv6 范围。** 如果不包含 IPv6 范围，会导致在以下两种情况下出现意外行为：

- 使用邮件客户端通过旧版身份验证连接到 Exchange Online 时，Azure AD 可能会收到 IPv6 地址。 初始身份验证请求转到 Exchange，然后通过代理发送到 Azure AD。
- 当在浏览器中使用 Outlook Web Access (OWA) 时，它将定期验证是否仍然满足所有条件访问策略。 此检查用于发现用户可能已从允许的 IP 地址移动到新位置（例如街上的咖啡店）的情况。 在这种情况下，如果使用的是 IPv6 地址并且 IPv6 地址不在配置的范围内，用户的会话可能会中断，并被定向回 Azure AD 进行重新验证。 

这些是可能需要在命名位置配置 IPv6 范围的最常见原因。 另外，如果使用的是 Azure VNet，会收到来自 IPv6 地址的流量。 如果有条件访问策略阻止了 VNet 流量，请检查 Azure AD 登录日志。 识别流量后，就可以获取正在使用的 IPv6 地址，并将其从策略中排除。 

> [!NOTE]
> 如果要为单个地址指定 IP CIDR 范围，请应用 /32 位掩码。 如果 IPv6 地址是 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a，并想从地址范围中排除该单一地址，应使用 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/32。

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>在 Azure AD 登录活动报告中标识 IPv6 流量

可以通过转到 [Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)来发现租户中的 IPv6 流量。 打开活动报告后，添加“IP 地址”列。 可通过此列标识 IPv6 流量。

还可以通过单击报告中的行，然后转到登录活动详细信息中的“位置”选项卡来查找客户端 IP。 

## <a name="what-you-should-know"></a>要点

### <a name="when-is-a-location-evaluated"></a>何时评估位置？

条件访问策略的评估时机：

- 当用户最初登录到 Web 应用、移动应用或桌面应用程序时。
- 当使用新式身份验证的移动应用或桌面应用程序使用刷新令牌来获取新的访问令牌时。 默认情况下此检查一小时进行一次。

对于使用新式身份验证的移动应用和桌面应用程序，此检查意味着，在更改网络位置的一小时内会检测到位置更改。 对于不使用新式身份验证的移动应用和桌面应用程序，此策略将应用于每个令牌请求。 请求的频率可能会因应用程序而异。 同样，对于 Web 应用程序，此策略在初始登录时应用，并适合用于 Web 应用程序的会话生存期。 由于不同应用程序的会话生存期不同，因此策略评估间隔的时间也会有所不同。 每次应用程序请求新的登录令牌时，就会应用一次此策略。

默认情况下，Azure AD 每小时颁发一个令牌。 在移出企业网络后的一小时内，将使用新式身份验证对应用程序实施该策略。

### <a name="user-ip-address"></a>用户 IP 地址

在策略评估中使用的 IP 地址是用户的公共 IP 地址。 对于专用网络中的设备，此 IP 地址不是 Intranet 中用户设备的客户端 IP，而是专用网络连接到公共 Internet 时使用的地址。

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>批量上传和下载命名位置

创建或更新命名位置时，若要进行批量更新，可以上传或下载含 IP 范围的 CSV 文件。 上传过程会将列表中的 IP 范围替换为该文件中的那些范围。 该文件的每行包含一个采用 CIDR 格式的 IP 地址范围。

### <a name="cloud-proxies-and-vpns"></a>云代理和 VPN

使用云托管代理或 VPN 解决方案时，Azure AD 在评估策略时使用的 IP 地址是该代理的 IP 地址。 不会使用包含用户公共 IP 地址的 X-Forwarded-For (XFF) 标头，因为没有任何机制验证该地址是否来自受信任的源，该标头可能提供了一种用于伪造 IP 地址的方法。

当云代理准备就绪时，可以使用一个策略来要求设备加入域，或使用来自 AD FS 的内部企业网络声明。

### <a name="api-support-and-powershell"></a>API 支持和 PowerShell

已命名位置的图形 API 预览版本可用。有关详细信息，请参阅[NAMEDLOCATION API](/graph/api/resources/namedlocation?view=graph-rest-beta)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅[构建条件访问策略](concept-conditional-access-policies.md)一文。
- 正在查找使用位置条件的示例策略？ 请参阅[条件访问：按位置阻止访问](howto-conditional-access-policy-location.md)一文
