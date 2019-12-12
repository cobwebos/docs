---
title: 嵌入 Internet 分析器客户端 | Microsoft Docs
description: 本文介绍如何在应用程序中嵌入 Internet 分析器 JavaScript 客户端。
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896386"
---
# <a name="embed-the-internet-analyzer-client"></a>嵌入 Internet 分析器客户端

本文介绍如何在应用程序中嵌入 JavaScript 客户端。 若要运行测试和接收记分卡分析，必须安装此客户端。 **配置第一个测试后提供配置文件特定的 JavaScript 客户端。** 在这里，你可以继续在该配置文件中添加或删除测试，而不必嵌入新脚本。 有关 Internet 分析器的详细信息，请参阅[概述](internet-analyzer-overview.md)。 

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

Internet 分析器需要拥有对 Azure 和其他 Microsoft 服务的访问权限才能正常工作。 在嵌入客户端之前，请允许对 `fpc.msedge.net` 和任何预配置的终结点 URL（通过 [CLI](internet-analyzer-cli.md) 可见）进行网络访问。

## <a name="find-the-client-script-url"></a>查找客户端脚本 URL

配置测试后，可以通过 Azure 门户或 Azure CLI 找到脚本 URL。 有关详细信息，请参阅[创建 Internet 分析器资源](internet-analyzer-create-test-portal.md)。

选项 1. 在 Azure 门户中，使用[此链接](https://aka.ms/InternetAnalyzerPreviewPortal)打开 Azure Internet 分析器的预览门户页。 通过转到“设置”>“配置”  ，转到 Internet 分析器配置文件以查看脚本 URL。

选项 2. 使用 Azure CLI 检查 `scriptFileUri` 属性。
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>客户端详细信息

脚本是专为你的配置文件和测试生成的。 加载后，该脚本将在 2 秒延迟后执行。 首先，它与 Internet 分析器服务联系以获取在测试中配置的终结点列表。 然后，它将运行指标，并将计时结果上传回 Internet 分析器服务。

## <a name="client-examples"></a>客户端示例

这些示例演示了将客户端 JavaScript 嵌入网页或应用程序中的几种基本方法。 我们使用 `0bfcb32638b44927935b9df86dcfe397` 作为脚本 URL 的示例配置文件 ID。

### <a name="run-on-page-load"></a>页面加载时运行
最简单的方法是使用 meta 标记块内的脚本标记。 此标记将在每次加载页面时执行脚本。

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>后续步骤

阅读 [Internet 分析器常见问题解答](internet-analyzer-faq.md)
