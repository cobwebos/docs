---
title: 必应自定义搜索：创建自定义搜索网页 | Microsoft Docs
description: 介绍如何配置自定义搜索实例并将其集成到网页
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365899"
---
# <a name="build-a-custom-search-web-page"></a>构建自定义搜索网页
借助必应自定义搜索，可以为关注的主题创建定制的搜索体验。 例如，如果拥有提供搜索体验的武术网站，则可以指定供必应搜索的域、子网站和网页。 用户会看到根据他们关注的内容定制的搜索结果，而不必浏览可能会包含不相关内容的常规搜索结果。 

本教程演示如何配置自定义搜索实例并将其集成到新网页中。

涵盖的任务包括：

> [!div class="checklist"]
> - 创建自定义搜索实例
> - 添加活动条目
> - 添加阻止条目
> - 添加固定条目
> - 将自定义搜索集成到网页中

## <a name="prerequisites"></a>先决条件
- 要继续学习本教程，需要 Bing 自定义搜索 API 的订阅密钥。  要获取密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)。
- 如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。

## <a name="create-a-custom-search-instance"></a>创建自定义搜索实例
要创建必应自定义搜索实例：

1.  打开 Internet 浏览器。
2.  导航到自定义搜索[门户](https://customsearch.ai)。
3.  使用 Microsoft 帐户 (MSA) 登录门户。 如果没有 MSA，请单击“创建 Microsoft 帐户”。 如果是首次使用门户，门户将请求数据访问权限。 单击 **“是”**。
4.  登录后，单击“新建自定义搜索”。 在“新建自定义搜索实例”窗口中，输入有意义的名称并说明搜索返回的内容类型。 可随时更改名称。
 
    ![“新建自定义搜索实例”框的屏幕截图](../media/newCustomSrch.png)

5.  单击“确定”，指定 URL 及是否包括基本页的子页：

    ![URL 定义页的屏幕截图](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>添加活动条目
要包含来自特定站点或 URL 的结果，可将其添加到“活动”选项卡。

1.  在“定义编辑器”，单击“活动”选项卡，输入要纳入搜索范围的一个或多个站点的 URL。

    ![“定义编辑器”的“活动”选项卡的屏幕截图](../media/customSrchEditor.png)

2.  若要确保实例会返回结果，请在右侧的预览窗格中输入查询。 必应仅返回已编入索引的公共站点的结果。

## <a name="add-blocked-entries"></a>添加阻止条目
要排除来自特定站点或 URL 的结果，可将其添加到“阻止”选项卡。

1. 在“定义编辑器”中，单击“阻止”选项卡，输入要从搜索范围排除的一个或多个站点的 URL。

    ![“定义编辑器”的“阻止”选项卡的屏幕截图](../media/blockedCustomSrch.png)


2. 若要确保实例不会从阻止的站点返回结果，请在右侧的预览窗格中输入查询。 

## <a name="add-pinned-entries"></a>添加固定条目
若要将特定网页固定到搜索结果顶部，可将该网页和查询术语添加到“固定”选项卡。“固定”选项卡包含网页和查询术语的配对列表，用于指定显示为特定查询最匹配结果的网页。 要使网页固定到顶部，用户的查询术语必须与固定的查询术语完全匹配。

1. 在“定义编辑器”中，单击“固定”选项卡，输入应作为最匹配结果返回的网页及其查询术语。

    ![“定义编辑器”的“固定”选项卡的屏幕截图](../media/pinnedCustomSrch.png)

2. 若要确认实例是否将指定网页作为最匹配结果返回，请在右侧预览窗格中输入固定的查询术语。

## <a name="configure-hosted-ui"></a>配置托管 UI
自定义搜索提供托管 UI，用于呈现自定义搜索实例的 JSON 响应。  要定义 UI 体验：

1. 单击“托管 UI”选项卡。
2. 选择布局。

    ![在“托管 UI”中选择布局这一步骤的屏幕截图](./media/custom-search-hosted-ui-select-layout.png)

3. 选择颜色主题。

    ![在“托管 UI”中选择布局这一步骤的屏幕截图](./media/custom-search-hosted-ui-select-color-theme.png)

4. 指定其他配置选项。

    ![在“托管 UI”中选择其他配置这一步骤的屏幕截图](./media/custom-search-hosted-ui-additional-configurations.png)

5. 粘贴“订阅密钥”。 请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api)。

    ![在“托管 UI”中选择其他配置这一步骤的屏幕截图](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>使用托管 UI
有两种使用托管 UI 的方法。  

- 选项 1：将提供的 JavaScript 代码片段集成到应用程序。
- 选项 2：使用提供的 HTML 终结点。

本教程的其余部分演示“选项 1：Javascript 代码片段”。  

## <a name="set-up-your-visual-studio-solution"></a>设置 Visual Studio 解决方案
1. 在计算机上打开 Visual Studio。
2. 在“文件”菜单中，选择“新建”，并选择“项目”。
3. 在“新建项目”窗口中，选择“Visual C#/Web/ASP.NET Core Web 应用程序”，为项目命名，然后单击“确定”。
   
    ![“新建项目”窗口屏幕截图](./media/custom-search-new-project.png)

4. 在“新建 ASP.NET Core Web 应用程序”窗口中，选择“Web 应用程序”，单击“确定”。
    
    ![“新建项目”窗口屏幕截图](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>编辑 index.cshtml
1. 在解决方案资源管理器中，展开“页面”，双击“index.cshtml”打开该文件。

    ![解决方案资源管理器屏幕截图，其中“页面”为展开状态，index.cshtml 为选中状态](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. 在 index.cshtml 中，删除自 7 行以下的所有内容。
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. 添加换行元素和 div，充当容器。

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. 从“托管 UI”选项卡上，向下滚动到名为“使用 UI”的部分。 复制 JavaScript 代码片段。

    ![“托管 UI”中“保存”按钮的屏幕截图](./media/custom-search-hosted-ui-consuming-ui.png)

5. 将脚本元素粘贴到添加的容器中。

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch">
        <script type="text/javascript"
                id="bcs_js_snippet"
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. 在解决方案资源管理器中，右键单击“wwwroot”并单击“在浏览器中查看”。

    ![在解决方案资源管理器的 wwwroot 上下文菜单中选择“在浏览器中查看”的屏幕截图](./media/custom-search-webapp-view-in-browser.png)

新的自定义搜索网页应类似于以下页面：

![自定义搜索网页的屏幕截图](./media/custom-search-webapp-browse-index.png)

执行搜索会呈现类似以下的结果。

![自定义搜索结果的屏幕截图](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>后续步骤
在本教程中，已完成以下内容：

> [!div class="checklist"]
> - 创建自定义搜索实例
> - 添加活动条目
> - 添加阻止条目
> - 添加固定条目
> - 将自定义搜索集成到网页中

现在可以开始以编程方式调用必应自定义搜索终结点。

> [!div class="nextstepaction"]
> [调用必应自定义搜索终结点 (C#)](../call-endpoint-csharp.md)