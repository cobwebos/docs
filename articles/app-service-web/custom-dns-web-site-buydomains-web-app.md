---
title: "如何在 Azure App Service Web Apps 中购买自定义域名"
description: "了解如何在 Azure App Service 中购买 Web 应用的自定义域名。"
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9c5c0b40d796130d93111545c93bedf86c374fd9


---
# <a name="buy-and-configure-a-custom-domain-name-in-azure-app-service"></a>在 Azure 应用服务中购买和配置自定义域名
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

当你创建 Web 应用时，Azure 会将其分配给 azurewebsites.net 的子域。 例如，如果 Web 应用名为 **contoso**，则 URL 为 **contoso.azurewebsites.net**。 Azure 还会将分配一个虚拟 IP 地址。

对于生产 Web 应用，你可能希望用户看到的自定义域名。 本文介绍如何使用[应用服务 Web 应用](http://go.microsoft.com/fwlink/?LinkId=529714)购买和配置自定义域。 

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## <a name="overview"></a>概述
如果 Web 应用没有域名，可以轻松地在 [Azure 门户](https://portal.azure.com/)中购买一个。 在购买过程中，你可以选择将 WWW 和根域的 DNS 记录自动映射到你的 Web 应用。 你也可以直接在 Azure 门户管理域权限。

请使用以下步骤来购买域名，然后将其分配给 Web 应用。

1. 在浏览器中，打开 [Azure 门户](https://portal.azure.com/)。
2. 在“Web 应用”选项卡中，单击 Web 应用的名称，选择“设置”，然后选择“自定义域”
   
    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)
3. 在“自定义域”边栏选项卡中，单击“购买域”。
   
    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)
4. 在“购买域”边栏选项卡中，使用文本框键入想要购买的域名，然后按 Enter。 将直接在文本框下方显示建议的可用域。 选择你想要购买什么域。 可以一次性选择购买多个域。 
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)
5. 单击“联系人信息”，然后填充域的联系人信息表单。
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)
   
   > [!NOTE]
   > 请务必正确填写所有必填字段，尤其是电子邮件地址。 如果购买的域没有“隐私保护”，在域生效之前，系统可能要求你验证电子邮件。 在某些情况下，如果联系人信息数据不正确，你将无法购买域。 
   > 
   > 
6. 现在你可以选择：
   
    a) 每年“自动续订”你的域
   
    b) 选择启用购买价格免费涵盖的“隐私保护”（注册机构不支持隐私保护的 TLD 除外。 例如 .co.in、.co.uk 等。）  
   
    c) 为 WWW 和根域“分配默认主机名”到当前 Web 应用。 
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
   
   > [!NOTE]
   > 选项 C 将为你自动配置 DNS 绑定和主机名绑定。  这样，购买过程一旦完成（除了少数情况下的 DNS 传播延迟），你的 Web 应用就可以访问自定义域。 如果 Web 应用在 Azure 流量管理器的后面，你将看不到分配根域的选项，因为 A 记录不能和流量管理器一起使用。 你随时可以通过一个 Web 应用将购买的域/子域分配到另一个 Web 应用，反之亦然。 有关详细信息，请参阅步骤 8。 
   > 
   > 
7. 单击“购买域”边栏选项卡上的“选择”，然后就会在“购买确认”边栏选项卡上看到购买信息。 如果接受法律条款，请单击“购买”，然后就会提交订单，可以在“通知”中监视购买流程。 购买域的过程可能需要几分钟才能完成。 
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)
8. 如果你成功订购了域，则可管理该域并将其分配给你的 Web 应用。 单击域右侧的“...”。 然后，可以“取消购买”或“管理域”。 单击“管理域”，然后可以在“管理域”边栏选项卡中将子域绑定到 Web 应用。 如果要将一个**子域**绑定到不同的 Web 应用，请从相应 Web 应用的上下文中执行此步骤。 在此处，你可以通过从下拉菜单中选择流量管理器名称，将域分配到流量管理器终结点（如果 Web 应用在流量管理器后面）。 通过执行此操作，域/子域将自动分配给该流量管理器终结点后面的所有 Web 应用。 
   
    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)
   
   > [!NOTE]
   > 你可以在 5 天内“取消购买”以获取全额退款。 5 天后你将无法“取消购买”，而是会看到“删除”域的选项。 删除域会在你的订阅中释放该域，并且该域将成为可用的域，在这种情况下不会向你退款。 
   > 
   > 

配置完成后，自定义域名将会列在 Web 应用的“主机名绑定”部分。

此时，你应该可以在浏览器中输入自定义域名，并查看它是否成功地将你转至 Web 应用。

## <a name="what-happens-to-the-custom-domain-you-bought"></a>购买的自定义域会发生什么情况
在“自定义域和 SSL”边栏选项卡中购买的自定义域绑定到 Azure 订阅。 尽管此自定义域是 Azure 资源，但它是独立的，与一开始需要购买域的那个应用服务应用没有关联。 这意味着：

* 在 Azure 门户中，可以将购买的自定义域提供给多个应用服务应用使用，而不是只能提供给一开始需要购买自定义域的那个应用。 
* 可以在 Azure 订阅中转到该订阅中*任何*应用服务应用的“自定义域及 SSL”边栏选项卡，管理购买的所有自定义域。
* 可以在同一个 Azure 订阅中，将任何应用服务应用分配到该自定义域中的子域。
* 如果决定要删除某个应用服务应用，可以选择不要删除与该应用绑定的自定义域，并将该自定义域提供给其他应用使用。

## <a name="if-you-cant-see-the-custom-domain-you-bought"></a>如果没看到自己购买的自定义域
如果在“自定义域和 SSL”边栏选项卡中购买了自定义域，但却没有在“管理域”下面看到该域，请确认以下事项：

* 自定义域的创建过程可能尚未完成。 请查看 Azure 门户顶部的通知铃铛来查看进度。
* 自定义域的创建可能由于某种原因而失败。 请查看 Azure 门户顶部的通知铃铛来查看进度。
* 自定义的域可能已成功创建，但边栏选项卡没有刷新。 请尝试重新打开“自定义域和 SSL”边栏选项卡。
* 可能在某个时间点删除了自定义域。 请从应用的主边栏选项卡单击“设置” > “审核日志”，查看审核日志。 
* 正在查看的“自定义域和 SSL”边栏选项卡可能属于不同 Azure 订阅中创建的应用。 请切换到不同订阅中的另一个应用，查看该应用的“自定义域和 SSL”边栏选项卡。  
    在门户中，除了该应用以外，无法查看或管理不同 Azure 订阅中创建的自定义域。 但是，如果在域的“管理域”边栏选项卡中单击“高级管理”，将会重定向到域提供商的网站，可在其中为不同 Azure 订阅中创建的应用手动配置自定义域，就像配置所有外部自定义域一样。********  [](web-sites-custom-domain-name.md) 
   




<!--HONumber=Nov16_HO3-->


