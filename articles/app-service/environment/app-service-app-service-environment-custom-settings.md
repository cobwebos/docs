---
title: 应用服务环境的自定义设置
description: 应用服务环境的自定义配置设置
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129509"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>应用服务环境的自定义配置设置
## <a name="overview"></a>概述
由于应用服务环境对单个客户是隔离的，因此有一些可专门应用于应用服务环境的配置设置。 本文介绍各种可用于应用服务环境的特定自定义设置。

如果没有应用服务环境，请参阅 [How to Create an App Service Environment](app-service-web-how-to-create-an-app-service-environment.md)（如何创建应用服务环境）。

可以在新的 **clusterSettings** 属性中使用数组存储应用服务环境自定义设置。 可以在 *hostingEnvironments* Azure 资源管理器实体的“Properties”字典中找到此属性。

以下简略的 Resource Manager 模板代码片段显示了 **clusterSettings** 属性：

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

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>使用 Azure 资源浏览器更新应用服务环境
也可以使用 [Azure 资源浏览器](https://resources.azure.com)更新应用服务环境。  

1. 在资源浏览器中，转到应用服务环境的节点（“订阅” > “resourceGroups” > “提供程序” > “Micrososft.Web” > “hostingEnvironments”）。 然后单击要更新的特定应用服务环境。
2. 在右窗格中，单击上方工具栏中的“读/写”，允许在资源浏览器中进行交互式编辑。  
3. 单击蓝色的“编辑”按钮，使 Resource Manager 模板可供编辑。
4. 滚动到右窗格底部。 **clusterSettings** 属性位于最底部，可以在此输入或更新其值。
5. 键入（或复制并粘贴）希望在 **clusterSettings** 属性中出现的配置值数组。  
6. 单击右窗格顶部的绿色“PUT”按钮，以提交对应用服务环境的更改。

不过，提交更改后，约需 30 分钟乘以应用服务环境中前端数量的时间，更改才会生效。
例如，如果应用服务环境有四个前端，大约需要两个小时才能完成配置更新。 实行配置更改时，就无法在应用服务环境中进行其他缩放操作或配置更改操作。

## <a name="disable-tls-10"></a>禁用 TLS 1.0
客户的常见问题是，尤其是处理 PCI 合规性审核的客户，如何显式禁用其应用的 TLS 1.0。

通过下列 **clusterSettings** 条目可以禁用 TLS 1.0：

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>更改 TLS 密码套件顺序
来自客户的另一个问题是，他们是否可以修改由其服务器协商的密码列表，而这可以通过修改 **clusterSettings** 来实现，如下所示。 可以从[此 MSDN 文章](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx)检索可用的密码套件列表。

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> 如果对 SChannel 无法理解的密码套件设置不正确的值，与服务器的所有 TLS 通信可能会停止运行。 在这种情况下，必须从 **clusterSettings** 中删除 *FrontEndSSLCipherSuiteOrder* 条目，并提交更新的 Resource Manager 模板以还原回默认的密码套件设置。  请谨慎使用此功能。
> 
> 

## <a name="get-started"></a>入门
Azure 快速入门 Resource Manager 模板站点包含具有[创建应用服务环境](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)基本定义的模板。

<!-- LINKS -->

<!-- IMAGES -->
