---
title: 安装本地数据网关 - Azure 逻辑应用
description: 在从 Azure 逻辑应用访问本地数据之前，下载并安装本地数据网关
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 09/01/2019
ms.openlocfilehash: 7384f058c82699095e1209e677dc5c6f61b57178
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309855"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>为 Azure 逻辑应用安装本地数据网关

在[从 Azure 逻辑应用连接到本地数据源](../logic-apps/logic-apps-gateway-connection.md)之前，请在本地计算机上下载并安装本地[数据网关](https://aka.ms/on-premises-data-gateway-installer)。 该网关充当桥，可在本地数据源和逻辑应用之间提供快速数据传输和加密。 可以将相同的网关安装与其他云服务（例如 Power BI、Microsoft Flow、PowerApps 和 Azure Analysis Services）结合使用。 有关如何使用这些服务的网关的信息，请参阅以下文章：

* [Microsoft Power BI 本地数据网关](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps 本地数据网关](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow 本地数据网关](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

本文介绍如何下载、安装和设置本地数据网关，以便可以从 Azure 逻辑应用访问本地数据源。 你还可以在本主题的后面部分了解有关[数据网关如何工作的](#gateway-cloud-service)详细信息。 有关网关的详细信息，请参阅[什么是本地网关](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)？

<a name="requirements"></a>

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

  * 必须使用相同的 Azure 帐户来安装和管理网关。 在安装过程中，你将使用此 Azure 帐户将你计算机上的网关与 Azure 订阅相关联。 稍后，在网关安装的 Azure 门户中创建 Azure 资源时，可以使用相同的 Azure 帐户。 

  * 你必须使用工作帐户或学校帐户（也称为*组织*帐户）登录，该帐户类似于`username@contoso.com`。 不能使用 Azure B2B （来宾）帐户或个人 Microsoft 帐户，如@hotmail.com或。 @outlook.com

    > [!TIP]
    > 如果注册了 Office 365 产品/服务，但未提供工作电子邮件地址，则地址可能类似于`username@domain.onmicrosoft.com`。 你的帐户存储在 Azure Active Directory （Azure AD）中的租户内。 大多数情况下，Azure AD 帐户的用户主体名称（UPN）与电子邮件地址相同。
    >
    > 若要使用与 Microsoft 帐户关联的[Visual Studio 标准订阅](https://visualstudio.microsoft.com/vs/pricing/)，请先[在 Azure AD 中创建租户](../active-directory/develop/quickstart-create-new-tenant.md)，或使用默认目录。 将具有密码的用户添加到该目录，然后向该用户提供对订阅的访问权限。 
    > 然后在网关安装期间可以使用此用户名和密码登录。

* 下面是本地计算机的要求：

  **最低要求**

  * .NET Framework 4.7。2
  * 64 位版本的 Windows 7 或 Windows Server 2008 R2（或更高版本）

  **建议的要求**

  * 8 核 CPU
  * 8 GB 内存
  * 64位版本的 Windows Server 2012 R2 或更高版本
  * 用于后台处理的固态硬盘（SSD）存储

  > [!NOTE]
  > 网关不支持 Windows Server Core。

* **相关注意事项**

  * 只能在本地计算机上安装本地数据网关，而不能在域控制器上安装。 但是，不一定要在数据源所在的同一台计算机上安装网关。 对于所有数据源，只需一个网关，因此不需要为每个数据源安装网关。

    > [!TIP]
    > 为了尽量降低延迟，可将网关安装在尽可能靠近数据源的位置或同一台计算机上（假设你有相应的权限）。

  * 在有线网络上的计算机上安装网关，将其连接到 internet，始终开机并且不会进入睡眠状态。 否则，网关将无法运行，并且性能可能会受到无线网络的影响。

  * 如果打算使用 Windows 身份验证，请确保在与数据源相同的 Active Directory 环境成员的计算机上安装网关。

  * 你为网关安装选择的区域与你稍后为逻辑应用创建 Azure 网关资源时必须选择的位置相同。 默认情况下，此区域与管理 Azure 帐户的 Azure AD 租户位于同一位置。 但是，你可以在安装网关的过程中更改该位置。

  * 网关有两种模式：标准模式和个人模式，仅适用于 Power BI。 在同一台计算机上, 不能有多个网关在同一模式下运行。

  * Azure 逻辑应用支持通过网关执行的写入操作，包括插入和更新。 但是，这些操作会[限制其负载大小](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>安装数据网关

1. [在本地计算机上下载并运行网关安装程序](https://aka.ms/on-premises-data-gateway-installer)。

1. 安装程序打开后，选择 "**下一步**"。

   ![安装程序简介](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. 选择 **"本地数据网关（推荐）** "，这是标准模式，然后选择 "**下一步**"。

   ![选择网关模式](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. 查看最低要求，保留默认的安装路径，接受使用条款，然后选择 "**安装**"。

   ![查看要求并接受使用条款](./media/logic-apps-gateway-install/accept-terms.png)

1. 成功安装网关后，提供组织帐户的电子邮件地址，然后选择 "**登录**"，例如：

   ![使用工作或学校帐户登录](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   你现在已登录到你的帐户。

1.  > 选择 **"在此计算机上注册新网关"** **。** 此步骤将你的网关安装注册到[网关云服务](#gateway-cloud-service)。

   ![注册网关](./media/logic-apps-gateway-install/register-gateway.png)

1. 提供网关安装的以下信息：

   * 在 Azure AD 租户中唯一的网关名称
   * 要使用的恢复密钥必须至少包含八个字符
   * 确认恢复密钥

   ![安装网关](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > 请将恢复密钥保存在安全位置。 如果要更改位置、移动、恢复或接管网关安装，则需要此密钥。

   请注意要**添加到现有网关群集**的选项，在为[高可用性方案](#high-availability)安装其他网关时选择此选项。

1. 检查网关云服务和[Azure 服务总线](https://azure.microsoft.com/services/service-bus/)的区域，该区域用于网关安装。 默认情况下，此区域与你的 Azure 帐户的 Azure AD 租户位于同一位置。

   ![检查区域](./media/logic-apps-gateway-install/check-region.png)

1. 若要接受默认区域，请选择 "**配置**"。 但是，如果默认区域不是最接近你的区域，则可以更改区域。

   *为何要更改网关安装的区域？*

   例如，为了降低延迟，可将网关的区域更改为逻辑应用所在的同一区域。 或者，可以选择最靠近本地数据源的区域。 *Azure 中的网关资源*和逻辑应用可以有不同的位置。

   1. 在当前区域的旁边，选择“更改区域”。

      ![更改区域](./media/logic-apps-gateway-install/change-region.png)

   1. 在下一页上，打开 "**选择区域**" 列表，选择所需的区域，然后选择 "**完成**"。

      ![选择其他区域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 查看最终确认窗口中的信息。 此示例对逻辑应用、Power BI、PowerApps 和 Microsoft Flow 使用同一帐户，因此该网关适用于所有这些服务。 准备就绪后，选择 "**关闭**"。

   ![已完成网关安装](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 现在，请[为网关安装创建 Azure 资源](../logic-apps/logic-apps-gateway-connection.md)。

## <a name="check-or-adjust-communication-settings"></a>检查或调整通信设置

本地数据网关依赖于适用于云连接的[Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)，并为与网关关联的 azure 区域建立相应的出站连接。 如果你的工作环境要求流量通过代理或防火墙来访问 internet，则此限制可能会阻止本地数据网关连接到网关云服务和 Azure 服务总线。 网关具有多个通信设置，你可以调整这些设置。 有关详细信息，请参阅以下主题：

* [调整本地数据网关的通信设置](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [为本地数据网关配置代理设置](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性支持

为了避免本地数据访问的单点故障，你可以在不同的计算机上安装多个网关（仅限标准模式），并将它们设置为群集或组。 这样一来，如果主网关不可用，数据请求将路由到第二个网关，依此类推。 因为你只能在计算机上安装一个标准网关，所以你必须在另一台计算机上安装群集中的每个其他网关。 使用本地数据网关的所有连接器都支持高可用性。

* 主网关所在的同一个 Azure 订阅中必须至少有一个网关安装，并且你能够提供该安装的恢复密钥。

* 主网关必须运行网关 2017 年 11 月更新版或更高版本。

设置主网关后，当你开始安装其他网关时，请选择 "**添加到现有网关群集**"，选择主网关，即你安装的第一个网关，并为该网关提供恢复密钥。 有关详细信息，请参阅[本地数据网关的高可用性群集](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>更改位置或者迁移、还原或接管现有网关

如果必须更改网关的位置、将网关安装移到新计算机、恢复已损坏的网关，或接管现有网关的所有权，需要使用安装网关期间提供的恢复密钥。

1. 在具有现有网关的计算机上运行网关安装程序。 如果没有最新的网关安装程序，请[下载最新的网关版本](https://aka.ms/on-premises-data-gateway-installer)。

   > [!NOTE]
   > 在安装了原始网关的计算机上还原网关之前，必须先卸载该计算机上的网关。 此操作会断开原来的网关。
   > 如果删除或删除任何云服务的网关群集，则无法恢复该群集。

1. 安装程序打开后，使用用于安装网关的同一 Azure 帐户登录。

1. 依次选择 "**迁移"、"还原" 或 "接管现有网关** >  **"，** 例如：

   ![选择“迁移、还原或接管现有网关”](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 从可用群集和网关中选择，并输入所选网关的恢复密钥，例如：

   ![选择网关](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. 若要更改区域，请选择 "**更改区域**"，然后选择新的区域。

1. 准备就绪后，请选择 "**配置**"，以完成任务。

## <a name="tenant-level-administration"></a>租户级别管理

若要查看 Azure AD 租户中的所有本地数据网关，该租户中的全局管理员可以以租户管理员身份登录到[Power Platform 管理中心](https://powerplatform.microsoft.com)，然后选择 "**数据网关**" 选项。 有关详细信息，请参阅[本地数据网关的租户级管理](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>重启网关

默认情况下，本地计算机上的网关安装以名为 "本地数据网关服务" 的 Windows 服务帐户的形式运行。 但是，网关安装使用其`NT SERVICE\PBIEgwService` "作为服务登录" 帐户凭据的名称，并具有 "作为服务登录" 权限。

> [!NOTE]
> Windows 服务帐户不同于用于连接到本地数据源的帐户，也不同于登录到云服务时使用的 Azure 帐户的帐户。

与任何其他 Windows 服务一样，您可以通过多种方式启动和停止该网关。 有关详细信息，请参阅[重启本地数据网关](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)。

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>网关的工作原理

你的组织中的用户可以访问他们已获得授权访问权限的本地数据。 但是，在这些用户可以连接到本地数据源之前，需要安装并设置本地数据网关。 通常，管理员是安装和设置网关的人员。 这些操作可能需要服务器管理员权限或有关本地服务器的特殊知识。

网关有助于在幕后通信后进行快速安全的通信。 此通信在云中的用户、网关云服务和本地数据源之间流动。 网关云服务可加密和存储数据源凭据与网关详细信息。 该服务还在用户、网关和本地数据源之间路由查询及其结果。

网关可与防火墙配合使用，只使用出站连接。 所有流量最初都是网关代理的安全出站流量。 网关通过[Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)中继加密通道上本地源中的数据。 此服务总线在网关与调用方服务之间创建通道，但不存储任何数据。 通过网关的所有数据经过加密。

![本地数据网关体系结构](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> 根据云服务，可能需要为网关设置数据源。

以下步骤描述与连接到本地数据源的元素交互时所发生的情况：

1. 云服务创建一个查询，以及数据源的加密凭据。 然后，该服务将查询和凭据发送到网关队列进行处理。

1. 网关云服务将分析该查询，并将请求推送到 Azure 服务总线。

1. Azure 服务总线会将挂起的请求发送到网关。

1. 网关获取查询，对凭据进行解密，并连接到一个或多个具有这些凭据的数据源。

1. 网关将查询发送到数据源以供运行。

1. 结果将从数据源发回给网关，并发送到网关云服务。 网关云服务随后使用结果。

### <a name="authentication-to-on-premises-data-sources"></a>对本地数据源进行身份验证

存储的凭据用于从网关连接到本地数据源。 无论使用哪种用户，网关都将使用存储的凭据进行连接。 对于特定服务（如 DirectQuery 和 LiveConnect），可能存在针对 Power BI 中 Analysis Services 的身份验证例外。

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft 云服务使用[Azure Active Directory （Azure AD）](../active-directory/fundamentals/active-directory-whatis.md)对用户进行身份验证。 Azure AD 租户包含用户名和安全组。 通常，用于登录的电子邮件地址与帐户的用户主体名称（UPN）相同。

### <a name="what-is-my-upn"></a>什么是 UPN？

如果你不是域管理员，你可能不知道你的 UPN。 若要查找帐户的 UPN，请从工作站`whoami /upn`运行命令。 尽管结果类似于电子邮件地址，但结果是本地域帐户的 UPN。

### <a name="synchronize-an-on-premises-active-directory-with-azure-active-directory"></a>使用 Azure Active Directory 同步本地 Active Directory

本地 Active Directory 帐户和 Azure AD 帐户的 UPN 必须相同。 因此，请确保每个本地 Active Directory 帐户都与 Azure AD 帐户相匹配。 云服务仅了解 Azure AD 中的帐户。 因此，无需将帐户添加到本地 Active Directory。 如果 Azure AD 中不存在该帐户，则不能使用该帐户。 

可以通过以下方式将本地 Active Directory 帐户与 Azure AD 相匹配。 

* 手动将帐户添加到 Azure AD。

  在 Azure 门户或 Microsoft 365 管理中心中创建帐户。 请确保帐户名称与本地 Active Directory 帐户的 UPN 匹配。

* 使用 Azure Active Directory Connect 工具将本地帐户同步到 Azure AD 租户。

  Azure AD Connect 工具提供目录同步和身份验证设置的选项。 这些选项包括密码哈希同步、传递身份验证和联合身份验证。 如果你不是租户管理员或本地域管理员，请联系你的 IT 管理员获取 Azure AD Connect 设置。 Azure AD Connect 确保 Azure AD UPN 与本地 Active Directory UPN 匹配。 如果你使用的是 Analysis Services 与 Power BI 或单一登录（SSO）功能之间的实时连接，则此匹配项将有所帮助。

  > [!NOTE]
  > 与 Azure AD Connect 工具同步帐户会在 Azure AD 租户中创建新帐户。

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>常见问题和故障排除

有关详细信息，请参阅以下主题：

* [本地数据网关常见问题解答](/data-integration/gateway/service-gateway-onprem-faq)
* [本地数据网关故障排除](/data-integration/gateway/service-gateway-tshoot)
* [监视和优化网关性能](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>后续步骤

* [从逻辑应用连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)
* [企业集成功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
