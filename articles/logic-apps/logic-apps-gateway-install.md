---
title: 安装本地数据网关
description: 在从 Azure 逻辑应用访问本地数据之前，下载并安装本地数据网关
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: a36b9d20fa20df56ec53e090976ea86e689ac74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322506"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>为 Azure 逻辑应用安装本地数据网关

在[从 Azure 逻辑应用连接到本地数据源](../logic-apps/logic-apps-gateway-connection.md)之前，请在本地计算机上下载并安装[本地数据网关](https://aka.ms/on-premises-data-gateway-installer)。 该网关充当一个桥梁，在本地数据源和逻辑应用之间进行快速的数据传输和加密。 可对其他云服务（例如 Power BI、Power Automate、Power Apps 和 Azure Analysis Services）使用相同的网关安装过程。 有关如何将网关用于这些服务的信息，请参阅以下文章：

* [Microsoft Power Automate 本地数据网关](/power-automate/gateway-reference)
* [Microsoft Power BI 本地数据网关](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 本地数据网关](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

本文介绍如何下载、安装和设置本地数据网关，以便可以从 Azure 逻辑应用访问本地数据源。 还可以在本主题的后面部分了解有关[数据网关工作原理](#gateway-cloud-service)的详细信息。 有关网关的详细信息，请参阅[什么是本地网关](/data-integration/gateway/service-gateway-onprem)？ 若要自动执行网关安装和管理任务，请访问 [DataGateway PowerShell cmdlet](https://www.powershellgallery.com/packages/DataGateway/3000.15.15) 的 PowerShell 库。

<a name="requirements"></a>

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有包含订阅的 Azure 帐户，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

  * 你的 Azure 帐户必须是工作帐户或学校帐户，如所示 `username@contoso.com` 。 不能使用 Azure B2B（来宾）帐户或个人 Microsoft 帐户，如 @hotmail.com 或 @outlook.com。

    > [!NOTE]
    > 如果你注册了 Microsoft 365 产品/服务，但未提供工作电子邮件地址，则你的地址可能类似于 `username@domain.onmicrosoft.com` 。 你的帐户存储在 Azure AD 租户中。 在大多数情况下，你的 Azure 帐户的用户主体名称 (UPN) 与你的电子邮件地址相同。

    若要使用与 Microsoft 帐户关联的 [Visual Studio Standard 订阅](https://visualstudio.microsoft.com/vs/pricing/) ，请首先 [创建 Azure AD 租户](../active-directory/develop/quickstart-create-new-tenant.md) 或使用默认目录。 将具有密码的用户添加到该目录，然后向该用户提供对 Azure 订阅的访问权限。 然后在网关安装期间可以使用此用户名和密码登录。

  * 你的 Azure 帐户必须仅属于单个 [Azure Active Directory (Azure AD) 租户或目录](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 需要使用同一 Azure 帐户在本地计算机上安装和管理网关。

  * 当你安装网关时，你可以通过 Azure 帐户登录，这会将网关安装链接到你的 Azure 帐户，并且仅将该帐户链接到该帐户。 不能将相同的网关安装链接到多个 Azure 帐户或 Azure AD 租户。

  * 稍后在 Azure 门户中，需要使用同一 Azure 帐户创建链接到网关安装的 Azure 网关资源。 只能将一个网关安装和一个 Azure 网关资源相互链接。 但是，Azure 帐户可以链接到每个与 Azure 网关资源关联的不同网关安装。 然后，你的逻辑应用可以在触发器和可访问本地数据源的操作中使用此网关资源。

* 下面是本地计算机的要求：

  **最低要求**

  * .NET Framework 4.7.2
  * 64 位版本的 Windows 7 或 Windows Server 2008 R2（或更高版本）

  **建议的要求**

  * 8 核 CPU
  * 8 GB 内存
  * 64 位版本的 Windows Server 2012 R2 或更高版本
  * 用于后台处理的固态硬盘 (SSD) 存储

  > [!NOTE]
  > 网关不支持 Windows Server Core。

* **相关注意事项**

  * 只在本地计算机上安装本地数据网关，而不在域控制器上安装。 不一定要在数据源所在的同一台计算机上安装网关。 所有数据源只需一个网关，因此，无需为每个数据源安装网关。

    > [!TIP]
    > 为了尽量降低延迟，可将网关安装在尽可能靠近数据源的位置或同一台计算机上（假设你有相应的权限）。

  * 将网关安装在有线网络上的本地计算机上，该计算机连接到 Internet，始终处于打开状态，并且不会进入休眠状态。 否则，网关将无法运行，并且性能在无线网络上可能会受到影响。

  * 如果你打算使用 Windows 身份验证，请确保在与数据源属于同一 Active Directory 环境的计算机上安装网关。

  * 选择用于安装网关的区域是稍后为逻辑应用创建 Azure 网关资源时必须选择的同一位置。 默认情况下，此区域是管理 Azure 帐户的 Azure AD 租户所在的同一位置。 但是，你可以在安装网关的过程中更改此位置。

  * 如果要更新网关安装，请先卸载当前的网关以获得更清晰的体验。

    最佳做法是确保使用受支持的版本。 Microsoft 每个月都会发布对本地数据网关的新的更新，目前仅支持本地数据网关的六个最新版本。 如果使用的版本出现问题，请尝试[升级到最新版本](https://aka.ms/on-premises-data-gateway-installer)，因为你的问题可能已在最新版本中得到解决。

  * 网关有两种模式：标准模式和个人模式，个人模式仅适用于 Power BI。 无法在同一台计算机上以相同模式运行多个网关。

  * Azure 逻辑应用支持通过网关进行读取和写入操作。 但是，这些操作存在[有效负载大小限制](/data-integration/gateway/service-gateway-onprem#considerations)。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>安装数据网关

1. [在本地计算机上下载并运行网关安装程序](https://aka.ms/on-premises-data-gateway-installer)。

1. 查看最低要求，保留默认的安装路径，接受使用条款，然后选择“安装”。

   ![查看要求并接受使用条款](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. 成功安装网关后，提供 Azure 帐户的电子邮件地址，然后选择“登录”，例如：

   ![使用工作或学校帐户登录](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   网关安装只能链接到一个 Azure 帐户。

1. 选择“在此计算机上注册新网关” > “下一步” 。 此步骤会将网关安装注册到[网关云服务](#gateway-cloud-service)。

   ![在本地计算机上注册网关](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. 提供网关安装的以下信息：

   * 在 Azure AD 租户中唯一的网关名称
   * 要使用的恢复密钥，必须至少包含八个字符
   * 确认恢复密钥

   ![提供网关安装的信息](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 请将恢复密钥保存在安全位置。 更改位置以及移动、恢复或接管网关安装时，都需要用到此密钥。

   请注意选项“添加到现有网关群集”。在为[高可用性方案](#high-availability)安装其他网关时，需选择此选项。

1. 检查网关云服务和网关安装使用的 [Azure 服务总线消息传送实例](../service-bus-messaging/service-bus-messaging-overview.md) 的区域。 默认情况下，此区域与 Azure 帐户的 Azure AD 租户位于同一位置。

   ![确认网关服务和服务总线的区域](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. 若要接受默认区域，请选择“配置”。 但是，如果默认区域不是最靠近你的区域，你可以更改区域。

   *为何要更改网关安装的区域？*

   例如，为了降低延迟，可将网关的区域更改为逻辑应用所在的同一区域。 或者，可以选择最靠近本地数据源的区域。 *Azure 中的网关资源*和逻辑应用可以有不同的位置。

   1. 在当前区域的旁边，选择“更改区域”。

      ![更改当前网关区域](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. 在下一页上打开“选择区域”列表，选择所需的区域，然后选择“完成” 。

      ![为网关服务选择其他区域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 查看最终确认窗口中的信息。 此示例对逻辑应用、Power BI、PowerApps 和 Power Automate 使用同一帐户，因此该网关适用于所有这些服务。 准备就绪后，请选择“关闭”。

   ![确认数据网关信息](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 现在[为网关安装创建 Azure 资源](../logic-apps/logic-apps-gateway-connection.md)。

## <a name="check-or-adjust-communication-settings"></a>检查或调整通信设置

本地数据网关依赖于 [Azure 服务总线消息传送](../service-bus-messaging/service-bus-messaging-overview.md) 进行云连接，并建立与网关关联的 azure 区域的相应出站连接。 如果你的工作环境要求流量通过代理或防火墙来访问 internet，则此限制可能会阻止本地数据网关连接到网关云服务和 Azure 服务总线消息传送。 网关有多个可以调整的通信设置。 有关详细信息，请参阅以下主题：

* [调整本地数据网关的通信设置](/data-integration/gateway/service-gateway-communication)
* [配置本地数据网关的代理设置](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性支持

为了避免在访问本地数据时出现单一故障点，可以在多个不同的计算机上各使用一个网关安装（仅限标准模式），并将这些安装设置为群集或组。 这样一来，如果主网关不可用，数据请求将路由到第二个网关，依此类推。 由于在一台计算机上只能安装一个标准网关，因此必须在另一台计算机上安装位于群集中的每个附加网关。 使用本地数据网关的所有连接器都支持高可用性。

* 你必须已至少安装一个网关，该网关使用与主网关相同的 Azure 帐户和恢复密钥。

* 主网关必须运行网关 2017 年 11 月更新版或更高版本。

设置主网关后，开始安装另一个网关时，请选择“添加到现有网关群集”，选择主网关（安装的第一个网关），然后为该网关提供恢复密钥。 有关详细信息，请参阅[本地数据网关的高可用性群集](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>更改位置或者迁移、还原或接管现有网关

如果必须更改网关的位置、将网关安装移到新计算机、恢复已损坏的网关，或接管现有网关的所有权，需要使用安装网关期间提供的恢复密钥。

> [!NOTE]
> 在安装了原始网关的计算机上还原网关之前，必须先卸载该计算机上的网关。 此操作会断开原始网关的连接。
> 如果删除任何云服务的网关群集，则无法恢复该群集。

1. 在具有现有网关的计算机上运行网关安装程序。

1. 安装程序打开后，使用用于安装网关的同一 Azure 帐户登录。

1. 选择“迁移、还原或接管现有网关” > “下一步”，例如： 

   ![选择“迁移、还原或接管现有网关”](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 从可用群集和网关中进行选择并输入所选网关的恢复密钥，例如：

   ![选择网关并提供恢复密钥](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. 若要更改区域，请选择“更改区域”，然后选择新区域。

1. 准备就绪后，选择“配置”以完成任务。

## <a name="tenant-level-administration"></a>租户级管理

若要洞察 Azure AD 租户中的所有本地数据网关，该租户中的全局管理员可以租户管理员的身份登录到 [Power Platform 管理中心](https://powerplatform.microsoft.com)，并选择“数据网关”选项。 有关详细信息，请参阅[本地数据网关的租户级管理](/data-integration/gateway/service-gateway-tenant-level-admin)。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>重启网关

默认情况下，本地计算机上的网关安装以名为“本地数据网关服务”的 Windows 服务帐户形式运行。 但是，网关安装对其“登录方式”帐户凭据使用 `NT SERVICE\PBIEgwService` 名称，并拥有“作为服务登录”权限。

> [!NOTE]
> Windows 服务帐户不同于用于连接到本地数据源的帐户，并且不同于登录到云服务时使用的 Azure 帐户。

与其他任何 Windows 服务一样，可以通过多种方式启动和停止网关。 有关详细信息，请参阅[重启本地数据网关](/data-integration/gateway/service-gateway-restart)。

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>网关的工作原理

在已获授权的情况下，组织中的用户可以访问本地数据。 但是，只有在你安装并设置本地数据网关之后，这些用户才能连接到你的本地数据源。 通常，网关由管理员安装和设置。 这些操作可能需要服务器管理员权限或有关本地服务器方面的专业知识。

网关有助于促进更快速、更安全的后台通信。 此通信在云中的用户、网关云服务和本地数据源之间流动。 网关云服务可加密和存储数据源凭据与网关详细信息。 该服务还会在用户、网关与本地数据源之间路由查询及其结果。

网关可与防火墙配合使用，只使用出站连接。 所有流量最初都是网关代理的安全出站流量。 网关通过 [Azure 服务总线消息传送](../service-bus-messaging/service-bus-messaging-overview.md)在加密通道上从本地源发送数据。 此服务总线在网关与调用方服务之间创建通道，但不存储任何数据。 通过网关的所有数据经过加密。

![本地数据网关的体系结构](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> 根据所用的云服务，可能需要为网关设置数据源。

这些步骤说明当你与连接到本地数据源的元素交互时会发生什么情况：

1. 云服务将创建查询，并为数据源创建加密的凭据。 然后，该服务将查询和凭据发送到网关队列进行处理。

1. 网关云服务将分析该查询，并将请求推送到 Azure 服务总线消息传送。

1. Azure 服务总线消息传送会将挂起的请求发送到网关。

1. 网关获取查询，对凭据进行解密，并使用这些凭据连接到一个或多个数据源。

1. 网关将查询发送到数据源以便运行。

1. 结果将从数据源发回给网关，并发送到网关云服务。 网关云服务随后使用结果。

### <a name="authentication-to-on-premises-data-sources"></a>对本地数据源进行身份验证

存储的凭据用于从网关连接到本地数据源。 不管用户是谁，网关都会使用存储的凭据来建立连接。 针对特定服务（如 Power BI 中适用于 Analysis Services 的 DirectQuery 和 LiveConnect）的身份验证可能存在例外情况。

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft 云服务使用 [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) 对用户进行身份验证。 Azure AD 租户包含用户名和安全组。 通常，用于登录的电子邮件地址与帐户的用户主体名称 (UPN) 相同。

### <a name="what-is-my-upn"></a>什么是 UPN？

如果你不是域管理员，你可能不知道自己的 UPN。 若要查找帐户的 UPN，请在工作站中运行 `whoami /upn` 命令。 尽管结果类似于电子邮件地址，但它是本地域帐户的 UPN。

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>将本地 Active Directory 与 Azure AD 同步

本地 Active Directory 帐户和 Azure AD 帐户的 UPN 必须相同。 因此，请确保每个本地 Active Directory 帐户与 Azure AD 帐户相匹配。 云服务仅知道 Azure AD 中的帐户。 因此，无需将帐户添加到本地 Active Directory。 如果 Azure AD 中不存在该帐户，则你无法使用该帐户。

可通过以下方式来使本地 Active Directory 帐户与 Azure AD 相匹配。

* 手动将帐户添加到 Azure AD。

  在 Azure 门户或 Microsoft 365 管理中心创建一个帐户。 确保帐户名称与本地 Active Directory 帐户的 UPN 相匹配。

* 使用 Azure Active Directory Connect 工具将本地帐户同步到 Azure AD 租户。

  Azure AD Connect 工具提供用于目录同步和身份验证设置的选项。 这些选项包括密码哈希同步、直通身份验证和联合身份验证。 如果你不是租户管理员或本地域管理员，请联系 IT 管理员来设置 Azure AD Connect。 Azure AD Connect 可确保 Azure AD UPN 与本地 Active Directory UPN 相匹配。 如果配合 Power BI 或单一登录 (SSO) 功能使用 Analysis Services 实时连接，则这种匹配将有所帮助。

  > [!NOTE]
  > 使用 Azure AD Connect 工具同步帐户会在 Azure AD 租户中创建新帐户。

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>常见问题解答和故障排除

* [本地数据网关常见问题解答](/data-integration/gateway/service-gateway-onprem-faq)
* [排查本地数据网关问题](/data-integration/gateway/service-gateway-tshoot)
* [监视和优化网关性能](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>后续步骤

* [从逻辑应用连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)
* [企业集成功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
