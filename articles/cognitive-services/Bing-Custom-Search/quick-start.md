---
title: 创建首个必应自定义搜索实例 - Microsoft 认知服务
description: 若要使用必应自定义搜索，需创建自定义搜索实例来定义 Web 视图或切片。 该实例包含指定必应要搜索的公共域、子网站和网页的设置以及各种排名调整。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 35f0bca01de1c2087f6ae30949cca9b03192b838
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365895"
---
# <a name="create-your-first-bing-custom-search-instance"></a>创建首个必应自定义搜索实例
若要使用必应自定义搜索，需创建自定义搜索实例来定义 Web 视图或切片。 该实例包含指定必应要搜索的公共域、网站和网页的设置以及各种排名调整。 若要创建实例，请使用必应自定义搜索[门户](https://customsearch.ai)。 

## <a name="create-a-custom-search-instance"></a>创建自定义搜索实例

要创建自定义搜索实例：

1.  获取自定义搜索 API 密钥。 请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)。
2.  使用 Microsoft 帐户 (MSA) 登录门户。 单击“登录”按钮。 如果是首次使用门户，请按照下方的其他步骤操作，如若不然，请继续执行步骤 3。
    - 如果没有 MSA，请单击“创建 Microsoft 帐户”。 门户会要求访问用户的数据。 单击 **“是”**。
    - 同意认知服务条款。 勾选“我同意”并单击“同意”。  
3.  登录后，单击“新建实例”并为实例命名。 请使用有意义的名称并描述搜索返回的内容类型。 可随时更改名称。 
4.  在“搜索体验”中的“活动”选项卡上，输入要纳入搜索范围的一个或多个站点的 URL。
5.  若要确认实例是否会返回结果，请在右侧的预览窗格中输入查询。 如果没有返回结果，请指定新站点。 必应仅返回已编入索引的公共站点的结果。
6.  单击“发布”，将配置更改发布到产品。 出现提示时，单击“发布”进行确认。
7.  依次单击“产品” > “终结点”，然后复制“自定义配置 ID”。 调用自定义搜索 API 时会用到此 ID。

## <a name="next-steps"></a>后续步骤

按照以下操作指南中的说明继续处理刚刚创建的自定义搜索实例：

- [配置自定义搜索体验](./define-your-custom-view.md)
- [调用自定义搜索](./search-your-custom-view.md)
- [共享自定义搜索](./share-your-custom-search.md)
- [配置托管 UI 体验](./hosted-ui.md)
- [使用修饰标记来突出显示文本](./hit-highlighting.md)
- [网页](./page-webpages.md)
