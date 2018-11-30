---
title: 站点搜索, 使用托管 UI 必应自定义搜索
titlesuffix: Azure Cognitive Services
description: 介绍如何配置必应自定义搜索托管 UI。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: c71597cf540cca67b9558ce28d20ce1d21ae0243
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424980"
---
# <a name="configure-your-hosted-ui-experience"></a>配置托管 UI 体验

配置自定义搜索实例后，可调用自定义搜索 API 来获取搜索结果并将其显示在应用中。 如果应用是 Web 应用，则可选择使用自定义搜索提供的托管 UI。   

## <a name="configure-custom-hosted-ui"></a>配置自定义托管 UI

要为 Web 应用配置托管 UI，请遵循以下步骤：

1. 登录自定义搜索[门户](https://customsearch.ai)。  
  
2. 单击自定义搜索实例。 若要创建实例，请参阅[创建首个必应自定义搜索实例](quick-start.md)。  

3. 单击“托管 UI”选项卡。  
  
4. 选择布局。
  
  - 搜索栏和结果(默认值) &mdash; 此布局是传统的搜索页面，包含搜索框和搜索结果。
  - 仅显示结果 &mdash; 此布局仅显示搜索结果。 而不会显示搜索框。 必须提供搜索查询，方法为将查询参数（&q=\<查询字符串>）添加到 JavaScript 片段或 HTML 终结点链接中的请求 URL。
  - 弹出式菜单 &mdash; 此布局提供搜索框，并在滑动覆盖区显示搜索结果。
      
5. 选择颜色主题。 可能的主题是： 
  
  - 经典
  - 深色
  - 天际蓝

  单击每个主题，查看最适合 Web 应用的主题类型。 如果需要微调颜色主题以更好地与 Web 应用集成，请单击“自定义主题”。 并非所有颜色配置都适用于所有布局主题。 若要更改颜色，请在相应的文本框中输入颜色的 RGB HEX 值（例如，#366eb8）。 或者，单击颜色按钮，然后单击适合你的阴影。 
  
  更改颜色后，请查看此更改对右侧的预览示例产生的影响。 始终可通过单击“重置为默认值”返回到所选主题的默认颜色。

  > [!NOTE]
  > 更改颜色主题时，请注重选择颜色时的可访问性。

5. 在“其他配置”下，根据应用提供相应的值。 这些设置是可选的。 若要查看应用或删除设置后的效果，请查看右侧的预览窗格。 可用配置有：  
  
  - Web 搜索配置：
    - 启用 Web 结果 &mdash; 确定是否启用 Web 搜索（可在页面顶部看到 Web 选项卡）。
    - 启用自动建议 &mdash; 确定是否启用了自定义自动建议（请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)了解其他费用）。
    - 每页的 Web 结果数 &mdash; 一次显示的 Web 搜索结果数（每页最多 50 条结果）。
    - 图像标题 &mdash; 确定搜索结果中是否显示图像。
  
    单击“显示高级配置”时将显示以下配置。  
  
    - 突出显示字词 &mdash; 确定是否在搜索结果中以粗体的形式显示搜索词。 
    - 链接目标 &mdash; 确定在用户单击某条搜索结果时，是否在新的浏览器选项卡（空白）或相同的浏览器选项卡（本身）中打开网页。 

  - 图像搜索配置：
    - 启用图像结果 &mdash; 确定是否启用图像搜索（可在页面顶部看到“图像”选项卡）。   
    - 每页的图像结果数 &mdash; 一次显示的图像搜索结果数（每页最多 150 条结果）。  
  
    单击“显示高级配置”时将显示以下配置。  
  
    - 启用筛选器 &mdash; 添加用户可用于筛选必应返回的图像的筛选器。 例如，用户可筛选仅动态 GIF 的结果。

  - 视频搜索配置：
    - 启用视频结果 &mdash; 确定是否启用视频搜索（可在页面顶部看到“视频”选项卡）。  
    - 每页的视频结果数 &mdash; 一次显示视频搜索结果数（每页最多 150 条结果）。
  
    单击“显示高级配置”时将显示以下配置。  
  
    - 启用筛选器 &mdash; 添加用户可用于筛选必应返回的视频的筛选器。 例如，用户可以筛选具有特定分辨率的视频或过去 24 小时内发现的视频结果。

  - 其他配置：
    - 页面标题 &mdash; 搜索结果页的标题区域中显示的文本（不适用于弹出式窗口布局）。
    - 工具栏主题 &mdash; 确定搜索结果页标题区域的背景色。  
  
    单击“显示高级配置”时将显示以下配置。  
  
    - 搜索框文本占位符 &mdash; 在进行输入前搜索框中显示的文本。
    - 标题链接 URL &mdash; 标题链接的目标。
    - 徽标 URl &mdash; 标题旁显示的图像。 
    - Favicon URL &mdash; 浏览器标题栏中显示的图标。  

    仅当通过 HTML 终结点使用托管 UI 时才应用以下配置（使用 JavaScript 片段时不适用）。
    
    - 页面标题
    - 工具栏主题
    - 标题链接 URL
    - 徽标 URL
    - Faviicon URL  
  
6. 输入搜索订阅密钥或从下拉列表中选择一个。 下拉列表的填充内容为帐户的 Azure 帐户订阅的密钥。 请参阅[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。  

7. 如果启用了自动建议，请输入自动建议订阅密钥，或从下拉列表进行选择。 下拉列表的填充内容为帐户的 Azure 帐户订阅的密钥。 自定义自动建议需要特定的订阅层，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)。

> [!NOTE]
> 在更改自定义托管 UI 配置时，右侧窗格会就所做更改提供可视参考。 显示的搜索结果不是实例的实际结果。

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>使用自定义 UI

要使用托管 UI，请执行以下任一操作： 

- 在网页中包含该脚本  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- 或者，在 Web 浏览器中使用以下 URL。   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > 根据需要将以下查询参数添加到 URL。 有关这些参数的信息，请参阅[自定义搜索 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) 参考。
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > 该页面无法显示隐私声明或其他注意事项和条款。 适用情况可能会有所不同。  

若要了解自定义配置 ID 等其他信息，请转到“产品”选项卡下的“终结点”。

## <a name="next-steps"></a>后续步骤

- [使用修饰标记来突出显示文本](./hit-highlighting.md)
- [网页](./page-webpages.md)
