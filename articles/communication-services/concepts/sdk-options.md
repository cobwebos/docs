---
title: 适用于 Azure 通信服务的客户端库和 REST Api
titleSuffix: An Azure Communication Services concept document
description: 详细了解 Azure 通信服务客户端库和 REST Api。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d5aba3638ce19c24841fe5b49b19b1139327b790
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578081"
---
# <a name="client-libraries-and-rest-apis"></a>客户端库和 REST API

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure 通信服务功能在概念上分为六个区域。 某些区域具有完全开源的客户端库。 调用客户端库使用专有网络接口，当前为关闭源，聊天库包含已关闭的源依赖项。 [Azure 通信服务 GitHub](https://github.com/Azure/communication)存储库中发布了客户端库的示例和其他技术详细信息。

## <a name="client-libraries"></a>客户端库

| 程序集               | 协议             |打开与关闭的源| 命名空间                          | 功能                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure 资源管理器 | REST | 打开            | Azure 的通信 | 设置和管理通信服务资源             |
| 通用                 | REST | 打开               | Azure。公共          | 提供其他客户端库的基类型 |
| 管理         | REST | 打开               | Azure. 管理  | 管理用户、访问令牌和电话号码，分配符合标准的 STUN 并打开服务器 |
| 聊天                   | 具有专用信号的 REST | 打开并关闭源信号包    | Azure 通信            | 向应用程序添加实时的基于文本的聊天  |
| SMS                    | REST | 打开              | Azure. Communication             | 发送和接收短信 |
| 调用                | 专用传输 | 已关闭 |Azure 通信。调用         | 利用语音、视频、屏幕共享和其他实时数据通信功能          |

请注意，Azure 资源管理器、管理和 SMS 客户端库侧重于服务集成，并且在许多情况下，如果将这些功能集成到最终用户应用程序，则会出现安全问题。 常用和聊天客户端库适用于服务和客户端应用程序。 调用客户端库专用于客户端应用程序。 面向服务方案的客户端库正在开发中。

### <a name="languages-and-publishing-locations"></a>语言和发布位置

下面详细介绍了单独的客户端库包的发布位置。 


| 区域           | JavaScript | .NET | Python | Java | Swift 或 Obj-C | Java (Android) | 其他                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure 资源管理器 | -         | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [通过 GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 通用         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)    | 不可用      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [通过 GitHub Swift](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| 管理 | [npm](https://www.npmjs.com/package/@azure/communication-administration)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| 聊天           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -  | -  | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 调用        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub-C via GitHub](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| 参考文档     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | 

## <a name="rest-apis"></a>REST API

与 [docs.microsoft.com](https://docs.microsoft.com/rest/api/azure/)中的其他 Azure REST api 一起介绍了通信服务 api。 本文档将说明如何构造 HTTP 消息，并提供使用 Postman 的指导。 此文档在 [GitHub](https://github.com/Azure/azure-rest-api-specs)上也以 Swagger 格式提供。

## <a name="additional-support-details"></a>其他支持详细信息

### <a name="ios-and-android-support-details"></a>iOS 和 Android 支持详细信息

- 通信服务 iOS 客户端库面向 iOS 版本 13 + 和 Xcode 11 +。
- Android Java 客户端库面向 Android API 级别 21 + 和 Android Studio 4.0 +

### <a name="net-support-details"></a>.NET 支持详细信息

除了调用以外，通信服务包的目标 .NET Standard 2.0 支持下列平台。

通过 .NET Framework 4.6.1 支持
- Windows 10、8.1、8和7
- Windows Server 2012 R2、2012和 2008 R2 SP1

通过 .NET Core 2.0 的支持：
- Windows 10 (1607 +) ，7 SP1 +，8。1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12 +
- Linux 多版本/发行版
- UWP 10.0.16299 (RS3) 2017 年9月
- Unity 2018。1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3。8

## <a name="api-stability-expectations"></a>API 稳定性期望 

> [!IMPORTANT]
> 本部分提供有关标记为 **稳定**的 REST api 和客户端库的指导。 在 **未通知的情况下**，可能更改或弃用标记为预发布、预览或 Beta 的 api。 目前，Azure 通信服务处于 **公共预览阶段**，api 被标记为这样。

将来，我们可能会停用通信服务客户端库的版本，我们可能会引入 REST Api 和发布的客户端库的重大更改。 Azure 通信服务 *通常* 会遵循两个可支持性策略来停用服务版本：

- 你将在需要更改代码之前至少三年通知你，因为通信服务接口发生了更改。 在停止接口之前，所有记录的 REST Api 和客户端库 Api 通常享有至少三年的警告。
- 你将至少获得一年通知，然后才能将客户端库程序集更新到最新次要版本。 这些必需的更新不需要更改任何代码，因为它们处于相同的主版本。 对于包含经常需要安全更新和性能更新的实时组件的调用和聊天库，尤其如此。 我们强烈建议你保持通信服务客户端库的更新。

### <a name="api-and-client-library-decommissioning-examples"></a>API 和客户端库解除授权示例

**已将 SMS REST API 的 v24 版本集成到你的应用程序中。Azure 通信版本 v25。**

在这些 Api 停止工作并强制更新到 v25 之前，你将获得3年警告。 此更新可能需要更改代码。

**已将调用客户端库的 v 2.02 版本集成到你的应用程序中。Azure 通信版本2.05。**

在 app-v 2.05 版本的12个月内，可能需要更新到2.05 版本的调用客户端库。 这应该是一个简单的项目替换，无需更改代码，因为 v 2.05 在 v2 主版本中且没有重大更改。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下客户端库概述：

- [调用客户端库概述](../concepts/voice-video-calling/calling-sdk-features.md)
- [聊天客户端库概述](../concepts/chat/sdk-features.md)
- [SMS 客户端库概述](../concepts/telephony-sms/sdk-features.md)

若要开始 Azure Communication Service：

- [创建 Azure 通信资源](../quickstarts/create-communication-resource.md)
- 生成 [用户访问令牌](../quickstarts/access-tokens.md)
