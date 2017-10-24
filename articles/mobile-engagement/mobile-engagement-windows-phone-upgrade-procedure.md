---
title: "Windows Phone Silverlight SDK 升级过程"
description: "Azure Mobile Engagement 的 Windows Phone Silverlight SDK 升级过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f87f65788075c7f4067e77946e1bcbc8f3709317
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK 升级过程
如果已将我们较旧版本的 SDK 集成到应用程序中，则在升级 SDK 时必须考虑以下几点。

如果错过了几个版本的 SDK，则可能需要执行多个过程。 例如，如果从 0.10.1 迁移至 0.11.0，则必须首先按照“从 0.9.0 至 0.10.1”的过程操作，然后按照“从 0.10.1 至 0.11.0”的过程操作。

## <a name="from-200-to-330"></a>从 2.0.0 至 3.3.0
### <a name="test-logs"></a>测试日志
现在可以启用/禁用/筛选由 SDK 生成的控制台日志。 要对其自定义，请将属性 `EngagementAgent.Instance.TestLogEnabled` 更新为 `EngagementTestLogLevel` 枚举中提供的值之一，例如：

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

## <a name="from-111-to-200"></a>从 1.1.1 至 2.0.0
以下部分介绍如何将 SDK 集成从由 Capptain SAS 提供的 Capptain 服务迁移到 Azure Mobile Engagement 支持的应用。 

> [!IMPORTANT]
> Capptain 和 Mobile Engagement 不是相同的服务，以下提供的过程仅重点描述如何迁移客户端应用。 迁移应用中的 SDK 不会将数据从 Capptain 服务器迁移到 Mobile Engagement 服务器
> 
> 

如果从较早版本进行迁移，请先参阅 Capptain 网站迁移到 1.1.1，再应用以下过程

### <a name="nuget-package"></a>NuGet 程序包
将 **Capptain.WindowsPhone** 替换为 **MicrosoftAzure.MobileEngagement** Nuget 程序包。

### <a name="applying-mobile-engagement"></a>应用 Mobile Engagement
SDK 使用术语 `Engagement`。 需要更新项目以匹配此更改。

需要卸载当前 Capptain NuGet 程序包。 请注意，Capptain 资源文件夹中的所有更改都会被删除。 如果想要保留这些文件，请对其进行备份。

之后，在项目上安装新的 Microsoft Azure Engagement nuget 程序包。 可直接在 [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement) 上找到它。 此操作将替换 Engagement 使用的所有资源文件，并将新的 Engagement DLL 添加到项目引用中。

需通过删除 Capptain DLL 引用来清理项目引用。 如果不执行此操作，Capptain 的版本会发生冲突并出现错误。

如果已自定义 Captain 资源，请复制旧文件内容并将其粘贴到新的 Engagement 文件中。 请注意，xaml 和 cs 文件都需要更新。

完成这些步骤后，只需将旧的 Capptain 引用替换为新的 Engagement 引用。

1. 所有 Capptain 命名空间均需进行更新。
   
    迁移前：
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    迁移后：
   
        using Microsoft.Azure.Engagement;
2. 包含“Capptain”的所有 Capptain 类都应包含“Engagement”。
   
    迁移前：
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    迁移后：
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Xaml 文件 Capptain 命名空间和属性也随之更改。
   
    迁移前：
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    迁移后：
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. 对于其他资源（如 Captain 图片），请注意，为使用“Engagement”，它们也已进行重命名。

### <a name="application-id--sdk-key"></a>应用程序 ID / SDK 密钥
Engagement 使用连接字符串。 无需使用 Mobile Engagement 指定应用程序 ID 和 SDK 密钥，只需指定连接字符串。 可在 EngagementConfiguration 文件中对其设置。

可在项目的 `Resources\EngagementConfiguration.xml` 文件中设置 Engagement 配置。

编辑此文件以指定：

* 标记 `<connectionString>` 和 `<\connectionString>` 间的应用程序连接字符串。

如果想在运行时指定连接字符串，则可以在 Engagement 代理初始化之前调用以下方法：

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

应用程序的连接字符串会显示在 Azure 经典门户中。

### <a name="items-name-change"></a>项名称更改
所有名为 *capptain* 的项被命名为 *engagement*。 同样，名为 *Capptain* 的项被命名为 *Engagement*。

常用 Capptain 项的示例：

* CapptainConfiguration 现在名为 EngagementConfiguration
* CapptainAgent 现在名为 EngagementAgent
* CapptainReach 现在名为 EngagementReach
* CapptainHttpConfig 现在名为 EngagementHttpConfig
* GetCapptainPageName 现在名为 GetEngagementPageName

请注意，重命名也会影响重写方法。

