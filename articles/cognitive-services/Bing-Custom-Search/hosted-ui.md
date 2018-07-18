---
title: 必应自定义搜索：站点搜索 | Microsoft Docs
description: 介绍如何配置托管 UI
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365893"
---
# <a name="configure-your-hosted-ui-experience"></a>配置托管 UI 体验
配置自定义搜索实例后，可调用自定义搜索 API 来获取搜索结果并将其显示在应用中。 如果应用是 Web 应用，则可选择使用自定义搜索提供的托管 UI。   

## <a name="configure-custom-hosted-ui"></a>配置自定义托管 UI
按照以下说明配置要包含在 Web 应用中的托管 UI。
1.  登录自定义搜索[门户](https://customsearch.ai)。
2.  单击自定义搜索实例。 若要创建实例，请参阅[创建首个必应自定义搜索实例](quick-start.md)。
3.  单击“托管 UI”选项卡。
4.  选择布局。
    - 搜索栏和结果（默认）&mdash;显示搜索框和搜索结果
    - 仅结果&mdash;不显示搜索框，仅显示结果
    - 弹出&mdash;不显示搜索框，仅以滑动覆盖形式显示结果
    
   > [!IMPORTANT]
   > 选择“仅结果”布局时，请务必将 customConfig 查询参数包含其中，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)。

5.  在“其他配置”下，根据应用提供相应的值。 这些设置是可选的。 若要查看应用或删除设置后的效果，请查看右侧的预览窗格。  可用配置有：
    - Web 搜索配置：
        - 启用的 Web 结果&mdash;确定是否返回了 Web 搜索结果
        - 启用自动建议&mdash;确定是否启用了自定义自动建议
        - 每页的 Web 结果&mdash;一次显示的 Web 搜索结果数
        - 图像标题&mdash;确定搜索结果中是否显示图像
        - 突出显示字词&mdash;确定是否在结果中以粗体显示搜索词
    - 图像搜索配置：
        - 启用的图像结果&mdash;确定是否返回了图像搜索结果
    - 其他配置：
        - 页面标题&mdash;页面标题区域显示的文本
        - 工具栏主题&mdash;确定页面标题区域的背景色
        - 搜索框文本占位符&mdash;在进行输入前搜索框中显示的文本
        - 标题链接 URL&mdash;标题链接的目标
        - 徽标 URl&mdash;标题旁显示的图像 
        - Favicon URL&mdash;浏览器标题栏中显示的图标

   > [!IMPORTANT]
   > 必须至少启用一个图像搜索或 Web 搜索。

6.  输入搜索订阅密钥，或从下拉列表中进行选择。 下拉列表的填充内容为帐户的 Azure 订阅的密钥。 请参阅[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。
7.  如果启用了自动建议，请输入自动建议订阅密钥，或从下拉列表进行选择。 下拉列表的填充内容为帐户的 Azure 订阅的密钥。 自定义自动建议需要特定的订阅层，请参阅[定价页面](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)。

> [!NOTE]
> 在更改自定义托管 UI 配置时，右侧窗格会就所做更改提供可视参考。 显示的搜索结果不是实例的实际结果

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>使用自定义 UI
要使用托管 UI，请执行以下任一操作： 

- 在网页中包含该脚本
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- 使用提供的 URL `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > 该页面无法显示隐私声明或其他注意事项和条款。 适用情况可能会有所不同。  

若要了解自定义配置 ID 等其他信息，请转到“产品”选项卡下的“终结点”。

## <a name="next-steps"></a>后续步骤
- [使用修饰标记来突出显示文本](./hit-highlighting.md)
- [网页](./page-webpages.md)