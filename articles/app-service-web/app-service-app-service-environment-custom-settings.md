<!-- not suitable for Mooncake -->

<properties
	pageTitle="应用服务环境的自定义设置"
	description="应用服务环境的自定义配置设置"
	services="app-service"
	documentationCenter=""
	authors="stefsch"
	manager="nirma"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	wacn.date=""
	ms.author="stefsch"/>

# 应用服务环境的自定义配置设置

## 概述 ##
由于应用服务环境对单个客户是隔离的，因此有一些可专门应用于应用服务环境的配置设置。本文介绍各种可用于应用服务环境的特定自定义设置。

如果没有应用服务环境，请参阅[如何创建应用服务环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment/)。

可以在新的 **clusterSettings** 属性中使用数组存储应用服务环境自定义设置。可以在 *hostingEnvironments* Azure Resource Manager 实体的“Properties”字典中找到此属性。

以下简略的 Resource Manager 模板代码段显示了 **clusterSettings** 属性：


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

**clusterSettings** 属性可以包含在 Resource Manager 模板中，以更新应用服务环境。

## 禁用 TLS 1.0 ##
客户的常见问题是，尤其是处理 PCI 合规性审核的客户，如何显式禁用其应用的 TLS 1.0。

通过下列 **clusterSettings** 条目可以禁用 TLS 1.0：

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## 更改 TLS 密码套件顺序 ##
来自客户的另一个问题是，他们是否可以修改由其服务器协商的密码列表，而这可以通过修改 **clusterSettings** 来实现，如下所示。可以从 [此 MSDN 文章](https://msdn.microsoft.com/zh-cn/library/windows/desktop/aa374757(v=vs.85).aspx) 检索可用的密码套件列表。

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  如果对 SChannel 无法理解的密码套件设置不正确的值，与服务器的所有 TLS 通信可能会停止运行。在这种情况下，必须从 **clusterSettings** 中删除 *FrontEndSSLCipherSuiteOrder* 条目，并提交更新的 Resource Manager 模板以还原回默认的密码套件设置。请谨慎使用此功能。

## 入门
Azure 快速入门 Resource Manager 模板站点包含具有[创建应用服务环境](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-create/)基本定义的模板。


<!-- LINKS -->

<!-- IMAGES -->

<!---HONumber=Mooncake_0926_2016-->