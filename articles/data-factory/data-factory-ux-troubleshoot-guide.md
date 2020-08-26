---
title: 排查 Azure 数据工厂 UX 问题
description: 了解如何对 Azure 数据工厂 UX 问题进行故障排除。
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567878"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>排查 Azure 数据工厂 UX 问题
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了 Azure 数据工厂 UX 的常见故障排除方法。

## <a name="adf-ux-not-loading"></a>ADF UX 未加载

> [!NOTE]
> Azure 数据工厂 UX 官方支持 Microsoft Edge 和 Google Chrome。 使用其他 web 浏览器可能导致意外或未记录的行为。

### <a name="third-party-cookies-blocked"></a>已阻止第三方 cookie

ADF UX 使用浏览器 cookie 来持久保存用户会话并实现交互式开发和监视体验。 你的浏览器可能会阻止第三方 cookie，因为你使用的是 incognito 会话或已启用 ad 阻止程序。 在加载门户时阻止第三方 cookie 可能会导致问题，例如，重定向到空白页， https://adf.azure.com/accesstoken.html 或收到警告消息，指出已阻止第三方 cookie。 若要解决此问题，请使用以下步骤在你的浏览器上启用第三方 cookie 选项：

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>允许所有 cookie

1. 在浏览器中访问**chrome://settings/cookies** 。
1. 选择 "**允许所有 cookie** " 选项 ![ Chrome-允许-全部 cookie](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. 刷新 ADF UX，然后重试。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>仅允许 ADF UX 使用 cookie
如果你不希望允许所有 cookie，则可以选择仅允许 ADF UX：
1. 请访问**chrome://settings/cookies**。
1. 单击 "**始终可以使用 cookie 的站点**" 下的 "**添加**"， ![ 将 "](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. 添加**adf.azure.com**站点，选中 "**所有 cookie** " 选项，然后选择 "保存"。 ![允许来自 ADF UX 网站的所有 cookie](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. 刷新 ADF UX，然后重试。


### <a name="microsoft-edge"></a>Microsoft Edge

1. 在浏览器中访问**edge://settings/content/cookies** 。
1. 确保已启用 "**允许网站保存和读取 cookie 数据**"，禁用 "**阻止第三方 cookie** ![ " 选项允许边缘中的所有 cookie](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. 刷新 ADF UX，然后重试。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>仅允许 ADF UX 使用 cookie

如果你不希望允许所有 cookie，则可以选择仅允许 ADF UX：

1. 请访问**edge://settings/content/cookies**。
1. 在 "**允许**" 部分，单击 "**添加**并添加**adf.azure.com**网站"。 ![将 ADF UX 添加到允许的站点](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. 刷新 ADF UX，然后重试。

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

* [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
* [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter 中有关数据工厂的信息](https://twitter.com/hashtag/DataFactory)
* [Azure 视频](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft 问答页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
