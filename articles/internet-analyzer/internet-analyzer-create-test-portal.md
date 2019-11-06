---
title: 使用门户创建 Internet 分析器测试| Microsoft Docs
description: 本文介绍如何创建第一个 Internet 分析器测试。
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509831"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>使用门户创建 Internet 分析器测试（预览版）

可通过两种方式创建 Internet 分析器资源：使用 [Azure 门户](internet-analyzer-cli.md)或使用 CLI。 本部分介绍如何使用门户体验创建新的 Azure Internet 分析器资源。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

公共预览版可在全球使用；但是，在预览期，数据存储仅限在“美国西部 2”区域使用。 

## <a name="basics"></a>基础

1. 按照 [Azure Internet 分析器常见问题解答](internet-analyzer-faq.md)的“如何参与预览？”中的说明获得 Internet 分析器的预览版访问权限  。
2. 在 [Azure 门户](https://preview.portal.azure.com)的主页中，单击“+ 创建资源”。  目前只能从 Azure 门户预览版获取 Internet 分析器。
3. 在“新建”页上的“搜索市场”字段中搜索“Internet 分析器”。  
4. 单击“Internet 分析器(预览版)”。  确保发行商为“Microsoft”，类别为“网络”。  
5. 在“Internet 分析器(预览版)”页上，单击“创建”打开“创建 Internet 分析器”页。   
6. 为 Internet 分析器资源指定以下配置设置：

    * **订阅：** 用于托管新 Internet 分析器资源的 Azure 订阅。 请使用用于请求预览版访问权限的同一订阅 ID。
    * **资源组：** 将在其中创建新 Internet 分析器资源的 Azure 资源组。 如果目前没有资源组，可以创建一个新资源组。
    * **名称：** 新 Internet 分析器资源配置文件的名称。
    * **区域：** 将在其中创建资源的 Azure 公共区域。 在预览期，只能选择“美国西部 2”。 

7. 指定完配置文件设置后，单击“查看 + 创建”。 

## <a name="configuration"></a>配置

必须先完成基本步骤，才能配置测试和嵌入 JavaScript 客户端。 创建配置文件后，转到“设置”>“配置”以配置第一个测试。 

1. 在“测试名称”框中指定测试的名称。 
2. 在“说明”字段中添加测试的说明。 
3. 单击“配置终结点”，随后右侧会显示一个选项卡。  选择要配置的终结点类型 - 单个 Azure 区域、多个 Azure 区域或自定义终结点。

    >
    预配置的终结点：单个 Azure 区域，以及多个 Azure 区域的组合
    * 从[预配置的 Azure 终结点列表](internet-analyzer-faq.md)中选择一个或一组区域。
    * 接下来，选择要评估的应用程序或内容分发体系结构的类型。
        * 单个 Azure 区域：站点加速 ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/))、静态内容缓存 ([Azure CDN for Microsoft](https://azure.microsoft.com/services/cdn/))，或“无”。
        * 多个 Azure 区域：站点加速 ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/))、DNS 定向（[Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)）  

    自定义终结点
    * 按照[创建自定义终结点](internet-analyzer-custom-endpoint.md)页上的说明操作。
    * 在门户中粘贴单一像素图像的 HTTPS URL 位置。
    >

4. 单击“添加”将终结点添加到测试。 
5. 重复步骤 1-4 配置第二个终结点。 终结点 B 始终是相对于终结点 A 度量的 - 配置终结点时，请考虑要将哪个终结点用作测试控件。
6. 单击“保存”按钮保存测试。  保存测试后，不再可以编辑特定测试的终结点。
7. 选择要启动的测试，然后单击“开始测试”。  随即会将测试的“状态”更改为“正在运行”。 随时可以开始测试，但是，必须嵌入 JavaScript 客户端才能让测试开始收集度量值。
8. 在任意位置添加更多测试。 请注意，只有在创建一个测试之后，才会生成唯一的 JavaScript 客户端。

## <a name="embed-client"></a>嵌入客户端

若要开始进行任何测试，必须在 Web 应用程序中嵌入 JavaScript 客户端。 至少配置一个测试后，单击“查看 + 创建”，转到“设置”>“配置”，然后复制 JavaScript 客户端。   可在[嵌入 Internet 分析器客户端](internet-analyzer-embed-client.md)页上找到具体的说明。  

## <a name="next-steps"></a>后续步骤

* 阅读 [Internet 分析器常见问题解答](internet-analyzer-faq.md)
* 详细了解如何嵌入 [Internet 分析器客户端](internet-analyzer-embed-client.md)和创建[自定义终结点](internet-analyzer-custom-endpoint.md)。
