---
title: "Azure IoT 套件常见问题解答 | Microsoft Docs"
description: "有关 IoT 套件的常见问题"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: corywink
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 8fa08ca6cefc10c1e1b47a7a498f69aca7de882e
ms.lasthandoff: 03/23/2017


---
# <a name="frequently-asked-questions-for-iot-suite"></a>有关 IoT 套件的常见问题

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>在哪里可以找到预配置解决方案的源代码？
源代码存储在以下 GitHub 存储库中：
* [远程监视预配置解决方案][lnk-remote-monitoring-github]
* [预见性维护预配置解决方案][lnk-predictive-maintenance-github]

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>如何更新到最新版的远程监视预配置解决方案，以便使用 IoT 中心设备管理功能？
* 如果从 https://www.azureiotsuite.com/ 站点部署预配置解决方案，则始终部署的是最新版解决方案的全新实例。
* 如果使用命令行部署预配置解决方案，则可使用新代码更新现有的部署。 请参阅 GitHub [存储库][lnk-remote-monitoring-github]中的[云部署][lnk-cloud-deployment]。

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>如何向远程监视预配置解决方案添加新设备方法的支持？
请参阅[自定义预配置解决方案][lnk-customize]一文的[向模拟器添加新方法的支持][lnk-add-method]部分。

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>模拟设备忽略所需属性更改，这是什么原因？
在远程监视预配置解决方案中，模拟设备代码仅使用所需的属性 **Desired.Config.TemperatureMeanValue** 和**Desired.Config.TelemetryInterval** 来更新报告的属性。 将忽略所有其他的所需属性更改请求。

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>我的设备未显示在解决方案仪表板的设备列表中，这是什么原因？
解决方案仪表板中的设备列表使用查询来返回设备列表。 目前，查询返回的设备数不能超过 10000。 可以尝试让查询的搜索条件更具限制性。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>在 Azure 门户中删除资源组与在 azureiotsuite.com 中对预配置解决方案单击删除之间的区别是什么？
* 如果在 [azureiotsuite.com][lnk-azureiotsuite] 中删除预配置解决方案，则会删除在创建预配置解决方案时预配的所有资源。 如果向资源组添加了其他资源，则也会删除这些资源。 
* 如果删除 [Azure 门户][lnk-azure-portal]中的资源组，则只会删除该资源组中的资源。 此外还需在 [Azure 经典门户][lnk-classic-portal]中删除与预配置的解决方案关联的 Azure Active Directory 应用程序。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>在一个订阅中可以设置多少个 IoT 中心实例？
默认情况下，可以[为每个订阅预配 10 个 IoT 中心][link-azuresublimits]。 可以创建 [Azure 支持票证][link-azuresupportticket]以提高此限制。 由于每个预配置的解决方案将预配一个新的 IoT 中心，因此，在给定的订阅中，最多只能预配 10 个预配置的解决方案。 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>在订阅中可以设置多少个 DocumentDB 实例？
50 个。 可以创建 [Azure 支持票证][link-azuresupportticket]以提高此限制，但在默认情况下，对每个订阅只能预配 50 个 DocumentDB 实例。 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>在订阅中可以设置多少个免费必应地图 API？
两个。 在一个 Azure 订阅中，只能创建两个面向企业的内部事务级别 1 必应地图计划。 默认情况下，远程监视解决方案是使用内部事务级别 1 计划预配的。 因此，如果不进行修改，则在订阅中只能设置最多两个远程监视解决方案。

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>我的远程监视解决方案部署具有静态地图，我如何添加交互式必应地图？
1. 从 [Azure 门户][lnk-azure-portal]获取用于企业的必应地图 API 查询密钥： 
   
   1. 在 [Azure 门户][lnk-azure-portal]中导航到用于企业的必应地图 API 所处的资源组。
   2. 单击“所有设置”，然后单击“密钥管理”。 
   3. 可以看到两个密钥：**主密钥**和**查询密钥**。 复制**查询密钥**的值。
      
      > [!NOTE]
      > 没有用于企业的必应地图 API 帐户？ 通过单击“+ 新建”、搜索用于企业的必应地图 API 并按照提示进行创建，在 [Azure 门户][lnk-azure-portal]中创建一个帐户。
      > 
      > 
2. 从 [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github] 下拉最新代码。
3. 在存储库的 /docs/ 文件夹中按照命令行部署指南运行本地或云部署。 
4. 运行了本地或云部署之后，在根文件夹中查找在部署过程中创建的 *.user.config 文件。 在文本编辑器中打开此文件。 
5. 更改以下行，以包含从**查询密钥**复制的值： 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>如果我具有 Microsoft Azure for DreamSpark，是否可以创建预配置解决方案？
当前无法使用 [Microsoft Azure for DreamSpark][lnk-dreamspark] 帐户创建预配置解决方案。 但是，可以在几分钟内创建一个 [Azure 免费试用帐户][lnk-30daytrial]，以便创建预配置的解决方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>如果我有云解决方案提供商 (CSP) 订阅，是否可以创建预配置的解决方案？
当前无法通过云解决方案提供商 (CSP) 订阅创建预配置的解决方案。 但是，可以在几分钟内创建一个 [Azure 免费试用帐户][lnk-30daytrial]，以便创建预配置的解决方案。

### <a name="how-do-i-delete-an-aad-tenant"></a>如何删除 AAD 租户？
请参阅 Eric Golpe 的博客文章[删除 Azure AD 租户的演练][lnk-delete-aad-tennant]。

### <a name="next-steps"></a>后续步骤
你还可以浏览 IoT 套件预配置的解决方案的一些其他特性和功能：

* [预见性维护预配置解决方案概述][lnk-predictive-overview]
* [从源头保障 IoT 的安全][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
