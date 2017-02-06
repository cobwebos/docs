---
title: "在 Azure 安全中心中添加 web 应用程序防火墙 | Microsoft 文档"
description: "本文档介绍如何实现 Azure 安全中心建议**添加 web 应用程序防火墙**和**完成应用程序保护**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2286437f4ab13384f895e906ccda48ac1b4c553d
ms.openlocfilehash: b44a0373ceca84b423984e01eee1e57a67d97cdd


---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>在 Azure 安全中心中添加 web 应用程序防火墙
为保护 web 应用程序，Azure 安全中心可能会建议从 Microsoft 合作伙伴添加 web 应用程序防火墙 (WAF)。 本文档将举例说明如何应用此建议。

为任何面向公众的 IP（实例级 IP 或负载均衡 IP）显示 WAF 建议，该 IP 具有与开放入站 Web 端口 (80,443) 关联的网络安全组。

安全中心建议设置 WAF，有助于防范针对虚拟机和应用服务环境上 Web 应用程序的攻击。 应用服务环境 (ASE) 是 Azure 应用服务的[高级](https://azure.microsoft.com/pricing/details/app-service/)服务计划选项，可提供完全隔离和专用的环境，以便安全地运行 Azure 应用服务应用。 若要了解有关 ASE 的详细信息，请参阅[应用服务环境文档](../app-service/app-service-app-service-environments-readme.md)。

> [!NOTE]
> 本文档通过使用示例部署介绍该服务。  本文档不是一份分步指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡上，选择“使用 web 应用程序防火墙保护 web 应用程序”。
   ![保护 web 应用程序][1]
2. 在“使用 web 应用程序防火墙保护 web 应用程序”边栏选项卡上，选择一个 web 应用程序。 “添加 Web 应用程序防火墙”边栏选项卡随即打开。
   ![添加 web 应用程序防火墙][2]
3. 可选择使用现有的 web 应用程序防火墙（如果可用）或创建新的防火墙。 此示例中没有任何现有可用的 WAF，因此需要创建 WAF。
4. 若要创建 WAF，请从集成合作伙伴列表中选择一个解决方案。 在此示例中，选择“Barracuda Web 应用程序防火墙”。
5. “Barracuda Web 应用程序防火墙”边栏选项卡会打开，提供有关该合作伙伴解决方案的信息。 在信息边栏选项卡选择“创建”。
   ![防火墙信息边栏选项卡][3]
6. 会打开“Web 应用程序防火墙”边栏选项卡，可在其中执行“VM 配置”步骤和提供“WAF 信息”。 选择“VM 配置”。
7. 在“VM 配置”边栏选项卡中输入启动要运行此 WAF 的虚拟机所需的信息。
   ![VM 配置][4]
8. 返回到“ Web 应用程序防火墙”边栏选项卡，然后选择“WAF 信息”。 在“WAF 信息”边栏选项卡中可对此 WAF 本身进行配置。 通过步骤 7 可对要在其上运行 WAF 的虚拟机进行配置，通过步骤 8 可对此 WAF 本身进行预配。

## <a name="finalize-application-protection"></a>完成应用程序保护
1. 返回到“建议”边栏选项卡。 创建此 WAF 后，会生成一个名为“完成应用程序保护”的新条目。 通过此条目，可知道需要完成将此 WAF 连接在 Azure 虚拟网络内的过程才可使其保护此应用程序。
   ![完成应用程序保护][5]
2. “完成应用程序保护”。 此时会打开一个新的边栏选项卡。 可看到有一个 web 应用程序需要路由其流量。
3. 选择此 web 应用程序。 会打开一个边栏选项卡，提供完成此 web 应用程序防火墙设置的步骤。 完成这些步骤，然后选择“限制”。 然后，安全中心会进行连接。
   ![限制流量][6]

> [!NOTE]
> 可以通过将应用程序添加到现有的 WAF 部署来保护安全中心中的多个 Web 应用程序。
>
>

此 WAF 的日志现已完整集成。 安全中心可开始自动收集和分析这些日志，以便显示重要的安全警报。

## <a name="see-also"></a>另请参阅
本文档演示了如何实现安全中心建议“添加 web 应用程序”。 若要了解有关配置 web 应用程序防火墙的详细信息，请参阅以下内容：

* [为应用服务环境配置 Web 应用程序防火墙 (WAF)](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png



<!--HONumber=Dec16_HO1-->


