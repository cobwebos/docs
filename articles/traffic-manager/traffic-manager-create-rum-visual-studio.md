---
title: 使用 Visual Studio Mobile Center 将实际用户度量发送到 Azure 流量管理器 | Microsoft Docs
description: 设置使用 Visual Studio Mobile Center 开发的移动应用程序，将实际用户度量发送到流量管理器
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 893e84b07b365fb0b534e0ddc021b2249c4174cf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181004"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>如何使用 Visual Studio Mobile Center 将实际用户度量发送到流量管理器

可以设置使用 Visual Studio Mobile Center 开发的移动应用程序，使用以下步骤将实际用户度量发送到流量管理器：

>[!NOTE]
> 目前，只有 Android 支持将实际用户度量发送到流量管理器。

若要配置实际用户度量，需要获取密钥并使用 RUM 包检测应用。

## <a name="step-1-obtain-a-key"></a>步骤 1：获取密钥
    
从客户端应用程序获取并发送到流量管理器的度量由使用唯一字符串的服务标识，称为实际用户度量 (RUM) 密钥。 可以使用 Azure 门户、REST API 或使用 PowerShell/CLI 接口获取 RUM 密钥。

使用以下过程通过 Azure 门户获取 RUM 密钥：
   1. 在浏览器中，登录 Azure 门户。 如果还没有帐户，可以注册免费一个月试用版。
   2. 在门户的搜索栏中，搜索要修改的流量管理器配置文件名称，并在显示的结果中单击该流量管理器配置文件。
   3. 在“流量管理器配置文件”页上，单击“设置”下的“实际用户度量”。
   4. 单击“生成密钥”新建 RUM 密钥。
        
   ![生成实际用户度量密钥](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **图 1：生成实际用户度量密钥**

   5.   该页显示生成的 RUM 密钥和需要嵌入到 HTML 页中的 JavaScript 代码片段。
 
   ![实际用户度量密钥的 Javascript 代码](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **图 2：实际用户度量密钥和度量 JavaScript**
 
   6. 单击“复制”按钮复制 RUM 密钥。 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>步骤 2：使用 Mobile Center SDK 的 RUM 包检测应用

如果不熟悉 Visual Studio Mobile Center，请访问其[网站](https://mobile.azure.com)。 有关 SDK 集成的详细说明，请参阅 [Android SDK 入门](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android)。

若要使用实际用户度量，请完成以下过程：

1.  向项目添加 SDK

    ATM RUM SDK 预览期间，需要显式引用程序包存储库。

    在 **app/build.gradle** 文件中添加以下行：

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    在 **app/build.gradle** 文件中添加以下行：

    ```groovy
    dependencies {   
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. 启动 SDK

    打开应用的主活动类并添加以下 import 语句：

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    在同一文件中查找 `onCreate` 回调并添加以下代码：

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>后续步骤
- 详细了解[实际用户度量](traffic-manager-rum-overview.md)
- 了解[流量管理器工作原理](traffic-manager-overview.md)
- 详细了解 [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [注册](https://mobile.azure.com) Mobile Center
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)

