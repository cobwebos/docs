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
ms.date: 01/04/2017
ms.author: corywink
translationtype: Human Translation
ms.sourcegitcommit: 14e2fcea9a6afbac640d665d5e44a700f855db4b
ms.openlocfilehash: ff2e371f0c75dca36cceda36f477e30588dea8e4


---
# <a name="frequently-asked-questions-for-iot-suite"></a>有关 IoT 套件的常见问题
### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>在 Azure 门户中删除资源组与在 azureiotsuite.com 中对预配置解决方案单击删除之间的区别是什么？
* 如果在 [azureiotsuite.com][lnk-azureiotsuite] 中删除预配置解决方案，则会删除在创建预配置解决方案时预配的所有资源。 如果向资源组添加了其他资源，则也会删除这些资源。 
* 如果删除 [Azure 门户][lnk-azure-portal]中的资源组，则只会删除该资源组中的资源。 此外还需在 [Azure 经典门户][lnk-classic-portal]中删除与预配置的解决方案关联的 Azure Active Directory 应用程序。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>在一个订阅中可以设置多少个 IoT 中心实例？
可以为每个订阅部署 10 个 IoT 中心。 可以创建 [Azure 支持票证][link-azuresupportticket]以提高此限制，但默认情况下，如 [Azure 订阅限制][link-azuresublimits]中所述，对每个订阅只能预配 10 个 IoT 中心。 由于每个预配置的解决方案将预配一个新的 IoT 中心，因此，在给定的订阅中，最多只能预配 10 个预配置的解决方案。 

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



<!--HONumber=Feb17_HO2-->


