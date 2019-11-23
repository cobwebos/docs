---
title: Install on-premises data gateway - Azure Logic Apps
description: 在从 Azure 逻辑应用访问本地数据之前，下载并安装本地数据网关
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: e1e56d18b0874a724849e28092ed46892a1b5519
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326378"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>为 Azure 逻辑应用安装本地数据网关

Before you can [connect to on-premises data sources from Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md), download and install the [on-premises data gateway](https://aka.ms/on-premises-data-gateway-installer) on a local computer. The gateway works as a bridge that provides quick data transfer and encryption between data sources on premises and your logic apps. You can use the same gateway installation with other cloud services, such as Power BI, Power Automate, Power Apps, and Azure Analysis Services. For information about how to use the gateway with these services, see these articles:

* [Microsoft Power Automate on-premises data gateway](/power-automate/gateway-reference)
* [Microsoft Power BI 本地数据网关](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps on-premises data gateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

This article shows how to download, install, and set up your on-premises data gateway so that you can access on-premises data sources from Azure Logic Apps. You can also learn more about [how the data gateway works](#gateway-cloud-service) later in this topic. For more information about the gateway, see [What is an on-premises gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)?

<a name="requirements"></a>

## <a name="prerequisites"></a>必备组件

* Azure 帐户和订阅。 If you don't have an Azure account with a subscription, [sign up for a free Azure account](https://azure.microsoft.com/free/).

  * Your Azure account must belong to a single [Azure Active Directory (Azure AD) tenant or directory](../active-directory/fundamentals/active-directory-whatis.md#terminology). You must use the same Azure account for installing and administering the gateway on your local computer.

  * During gateway installation, you sign in with your Azure account, which links your gateway installation to your Azure account and only that account. Later, in the Azure portal, you must use the same Azure account and Azure AD tenant when you create an Azure gateway resource that registers and claims your gateway installation. In Azure Logic Apps, on-premises triggers and actions then use the gateway resource for connecting to on-premises data sources.

    > [!NOTE]
    > You can link only one gateway installation and one Azure gateway resource to each other. You can't link the same gateway installation to multiple Azure accounts or Azure gateway resources. However, an Azure account can link to multiple gateway installations and Azure gateway resources. In an on-premises trigger or action, you can select from your various Azure subscriptions, and then select an associated gateway resource.

  * You need to sign in with either a work account or school account, also known as an *organization* account, which looks like `username@contoso.com`. You can't use Azure B2B (guest) accounts or personal Microsoft accounts, such as @hotmail.com or @outlook.com.

    > [!TIP]
    > If you signed up for an Office 365 offering and didn't provide your work email address, your address might look like `username@domain.onmicrosoft.com`. Your account is stored within a tenant in an Azure Active Directory (Azure AD). In most cases, the User Principal Name (UPN) for your Azure AD account is the same as your email address.
    >
    > To use a [Visual Studio Standard subscription](https://visualstudio.microsoft.com/vs/pricing/) that's linked to a Microsoft account, first [create a tenant in Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) or use the default directory. Add a user with a password to the directory, and then give that user access to your Azure subscription. 然后在网关安装期间可以使用此用户名和密码登录。

* 下面是本地计算机的要求：

  **最低要求**

  * .NET Framework 4.7.2
  * 64 位版本的 Windows 7 或 Windows Server 2008 R2（或更高版本）

  **建议的要求**

  * 8 核 CPU
  * 8 GB 内存
  * 64-bit version of Windows Server 2012 R2 or later
  * Solid-state drive (SSD) storage for spooling

  > [!NOTE]
  > The gateway doesn't support Windows Server Core.

* **Related considerations**

  * Install the on-premises data gateway only on a local computer, not a domain controller. 不一定要在数据源所在的同一台计算机上安装网关。 You need only one gateway for all your data sources, so you don't need to install the gateway for each data source.

    > [!TIP]
    > 为了尽量降低延迟，可将网关安装在尽可能靠近数据源的位置或同一台计算机上（假设你有相应的权限）。

  * Install the gateway on a computer that's on a wired network, connected to the internet, always turned on, and doesn't go to sleep. Otherwise, the gateway can't run, and performance might suffer over a wireless network.

  * If you plan to use Windows authentication, make sure that you install the gateway on a computer that's a member of the same Active Directory environment as your data sources.

  * The region that you select for your gateway installation is the same location that you must select when you later create the Azure gateway resource for your logic app. By default, this region is the same location as your Azure AD tenant that manages your Azure account. However, you can change the location during gateway installation.

  * If you're updating your gateway installation to the latest version, uninstall your current gateway first for a cleaner experience.

  * The gateway has two modes: standard mode and personal mode, which applies only to Power BI. You can't have more than one gateway running in the same mode on the same computer.

  * Azure Logic Apps supports read and write operations through the gateway. However, these operations have [limits on their payload size](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>安装数据网关

1. [在本地计算机上下载并运行网关安装程序](https://aka.ms/on-premises-data-gateway-installer)。

1. After the installer opens, select **Next**.

   ![Intro screen for gateway installer](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Select **On-premises data gateway (recommended)** , which is standard mode, and then select **Next**.

   ![Select run mode for data gateway](./media/logic-apps-gateway-install/select-gateway-running-mode.png)

1. Review the minimum requirements, keep the default installation path, accept the terms of use, and then select **Install**.

   ![Review requirements and accept terms of use](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. After the gateway successfully installs, provide the email address for your Azure account, and then select **Sign in**, for example:

   ![使用工作或学校帐户登录](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Your gateway installation can link to only one Azure account.

1. Select **Register a new gateway on this computer** > **Next**. This step registers your gateway installation with the [gateway cloud service](#gateway-cloud-service).

   ![Register gateway on local computer](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. 提供网关安装的以下信息：

   * A gateway name that's unique across your Azure AD tenant
   * The recovery key, which must have at least eight characters, that you want to use
   * 确认恢复密钥

   ![Provide information for gateway installation](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 请将恢复密钥保存在安全位置。 You need this key if you ever want to change the location, move, recover, or take over a gateway installation.

   Note the option to **Add to an existing gateway cluster**, which you select when you install additional gateways for [high-availability scenarios](#high-availability).

1. Check the region for the gateway cloud service and [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) that's used by your gateway installation. By default, this region is the same location as the Azure AD tenant for your Azure account.

   ![Confirm region for gateway service and service bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. To accept the default region, select **Configure**. However, if the default region isn't the one that's closest to you, you can change the region.

   *为何要更改网关安装的区域？*

   例如，为了降低延迟，可将网关的区域更改为逻辑应用所在的同一区域。 或者，可以选择最靠近本地数据源的区域。 *Azure 中的网关资源*和逻辑应用可以有不同的位置。

   1. 在当前区域的旁边，选择“更改区域”。

      ![Change the current gateway region](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. On the next page, open the **Select Region** list, select the region you want, and select **Done**.

      ![Select another region for gateway service](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Review the information in the final confirmation window. This example uses the same account for Logic Apps, Power BI, Power Apps, and Power Automate, so the gateway is available for all these services. When you're ready, select **Close**.

   ![Confirm data gateway information](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Now [create the Azure resource for your gateway installation](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Check or adjust communication settings

The on-premises data gateway depends on [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) for cloud connectivity and establishes the corresponding outbound connections to the gateway's associated Azure region. If your work environment requires that traffic goes through a proxy or firewall to access the internet, this restriction might prevent the on-premises data gateway from connecting to the gateway cloud service and Azure Service Bus. The gateway has several communication settings, which you can adjust. 有关详细信息，请参阅以下主题：

* [Adjust communication settings for the on-premises data gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Configure proxy settings for the on-premises data gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性支持

To avoid single points of failure for on-premises data access, you can have multiple gateway installations (standard mode only) with each on a different computer, and set them up as a cluster or group. That way, if the primary gateway is unavailable, data requests are routed to the second gateway, and so on. Because you can install only one standard gateway on a computer, you must install each additional gateway that's in the cluster on a different computer. All the connectors that work with the on-premises data gateway support high availability.

* You must already have at least one gateway installation with the same Azure account as the primary gateway and the recovery key for that installation.

* 主网关必须运行网关 2017 年 11 月更新版或更高版本。

After you set up your primary gateway, when you go to install another gateway, select **Add to an existing gateway cluster**, select the primary gateway, which is the first gateway that you installed, and provide the recovery key for that gateway. 有关详细信息，请参阅[本地数据网关的高可用性群集](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>更改位置或者迁移、还原或接管现有网关

如果必须更改网关的位置、将网关安装移到新计算机、恢复已损坏的网关，或接管现有网关的所有权，需要使用安装网关期间提供的恢复密钥。

1. Run the gateway installer on the computer that has the existing gateway. If you don't have the latest gateway installer, [download the latest gateway version](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Before you restore the gateway on the computer that has the original gateway installation, you must first uninstall the gateway on that computer. This action disconnects the original gateway.
   > If you remove or delete a gateway cluster for any cloud service, you can't restore that cluster.

1. After the installer opens, sign in with the same Azure account that was used to install the gateway.

1. Select **Migrate, restore, or takeover an existing gateway** > **Next**, for example:

   ![选择“迁移、还原或接管现有网关”](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Select from the available clusters and gateways, and enter the recovery key for the selected gateway, for example:

   ![Select gateway and provide recovery key](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. To change the region, select **Change Region**, and select the new region.

1. When you're ready, select **Configure** so that you can finish your task.

## <a name="tenant-level-administration"></a>Tenant-level administration

To get visibility into all the on-premises data gateways in an Azure AD tenant, global administrators in that tenant can sign in to the [Power Platform Admin center](https://powerplatform.microsoft.com) as a tenant administrator and select the **Data Gateways** option. For more information, see [Tenant-level administration for the on-premises data gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>重启网关

By default, the gateway installation on your local computer runs as a Windows service account named "On-premises data gateway service". However, the gateway installation uses the `NT SERVICE\PBIEgwService` name for its "Log On As" account credentials and has "Log on as a service" permissions.

> [!NOTE]
> Your Windows service account differs from the account used for connecting to on-premises data sources and from the Azure account that you use when you sign in to cloud services.

Like any other Windows service, you can start and stop the gateway in various ways. For more information, see [Restart an on-premises data gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>网关的工作原理

Users in your organization can access on-premises data for which they already have authorized access. However, before these users can connect to your on-premises data source, you need to install and set up an on-premises data gateway. Usually, an admin is the person who installs and sets up a gateway. These actions might require Server Administrator permissions or special knowledge about your on-premises servers.

The gateway facilitates quick and secure communication behind-the-scenes-communication. This communication flows between a user in the cloud, the gateway cloud service, and your on-premises data source. 网关云服务可加密和存储数据源凭据与网关详细信息。 The service also routes queries and their results between the user, the gateway, and your on-premises data source.

网关可与防火墙配合使用，只使用出站连接。 所有流量最初都是网关代理的安全出站流量。 The gateway relays data from on-premises sources on encrypted channels through [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). 此服务总线在网关与调用方服务之间创建通道，但不存储任何数据。 通过网关的所有数据经过加密。

![Architecture for on-premises data gateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Depending on the cloud service, you might need to set up a data source for the gateway.

These steps describe what happens when you interact with an element that's connected to an on-premises data source:

1. The cloud service creates a query, along with the encrypted credentials for the data source. The service then sends the query and credentials to the gateway queue for processing.

1. The gateway cloud service analyzes the query and pushes the request to Azure Service Bus.

1. Azure Service Bus sends the pending requests to the gateway.

1. The gateway gets the query, decrypts the credentials, and connects to one or more data sources with those credentials.

1. The gateway sends the query to the data source for running.

1. 结果将从数据源发回给网关，并发送到网关云服务。 网关云服务随后使用结果。

### <a name="authentication-to-on-premises-data-sources"></a>Authentication to on-premises data sources

A stored credential is used to connect from the gateway to on-premises data sources. Regardless of the user, the gateway uses the stored credential to connect. There might be authentication exceptions for specific services, such as DirectQuery and LiveConnect for Analysis Services in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft cloud services use [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) to authenticate users. An Azure AD tenant contains usernames and security groups. Typically, the email address that you use for sign-in is the same as the User Principal Name (UPN) for your account.

### <a name="what-is-my-upn"></a>What is my UPN?

If you're not a domain admin, you might not know your UPN. To find the UPN for your account, run the `whoami /upn` command from your workstation. Although the result looks like an email address, the result is the UPN for your local domain account.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synchronize an on-premises Active Directory with Azure AD

The UPN for your on-premises Active Directory accounts and Azure AD accounts must be the same. So, make sure that each on-premises Active Directory account matches your Azure AD account. The cloud services know only about accounts within Azure AD. So, you don't need to add an account to your on-premises Active Directory. If the account doesn't exist in Azure AD, you can't use that account.

Here are ways that you can match your on-premises Active Directory accounts with Azure AD.

* Add accounts manually to Azure AD.

  Create an account in the Azure portal or in the Microsoft 365 admin center. Make sure that the account name matches the UPN for the on-premises Active Directory account.

* Synchronize local accounts to your Azure AD tenant by using the Azure Active Directory Connect tool.

  The Azure AD Connect tool provides options for directory synchronization and authentication setup. These options include password hash sync, pass-through authentication, and federation. If you're not a tenant admin or a local domain admin, contact your IT admin to get Azure AD Connect set up. Azure AD Connect ensures that your Azure AD UPN matches your local Active Directory UPN. This matching helps if you're using Analysis Services live connections with Power BI or single sign-on (SSO) capabilities.

  > [!NOTE]
  > Synchronizing accounts with the Azure AD Connect tool creates new accounts in your Azure AD tenant.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>FAQ and troubleshooting

有关详细信息，请参阅以下主题：

* [本地数据网关常见问题解答](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Troubleshoot the on-premises data gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitor and optimize gateway performance](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>后续步骤

* [从逻辑应用连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)
* [企业集成功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
