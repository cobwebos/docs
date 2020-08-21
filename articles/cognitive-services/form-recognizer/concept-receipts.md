---
title: 回执-窗体识别器
titleSuffix: Azure Cognitive Services
description: 了解与采用格式识别器 API （使用和限制）相关的接收分析相关概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724955"
---
# <a name="receipt-concepts"></a>接收概念

AzureForm 识别器可以使用它的一个预生成模型分析回执。 接收方 API 从销售收据提取关键信息，如商家名称、交易日期、交易总计、行项等。 

## <a name="understanding-receipts"></a>了解回执 

许多企业和个人仍然依赖于从其销售收据手动提取数据，无论是出于业务开支报告、偿还、税收目的、预算还是其他目的。 通常，在这种情况下，需要提供物理收据的图像来进行验证。  

自动从这些收据提取数据可能会很复杂。 收据可能 crumpled，难于阅读，智能手机图像的收据质量可能较低。 另外，收据模板和字段在市场、区域和商家上可能会有很大的差异。 数据提取和现场检测中的这些挑战都使接收处理成为一个独特的问题。  

使用光学字符识别 (OCR) 和预生成的接收模型，接收 API 可实现这些接收处理方案。 因为模型是针对我们的数据预先训练的，所以可以在一步中轻松分析收据 &mdash; 无需模型定型或标记。

![示例回执](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>回执 API 有什么作用？ 

预生成的回执 API 提取销售回执的内容， &mdash; 即您通常会获得餐馆、零售商或杂货店的收据类型。

### <a name="fields-extracted"></a>提取的字段

* 商家名称 
* 商家地址 
* 商家电话号码 
* 事务日期 
* Transaction Time（事务时间） 
* 小计 
* 税款 
* 总计 
* 提示 
* 行项提取 (例如，项数量、项价格、项名称) 

### <a name="additional-features"></a>其他功能

接收 API 还会返回以下信息：

* 收据类型 (如明细化、信用卡等) 
* 字段置信度 (每个字段都返回关联的置信度值) 
* OCR raw 文本 (用于整接收的 OCR 提取文本输出) 

## <a name="input-requirements"></a>输入要求

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支持的区域设置 

* **预先构建的接收** V2.0 (GA) 支持 en-us 区域设置中的销售收据
* **预先生成的收条2.1 版-预览版** (公开预览版) 增加了对以下 EN 收据区域设置的其他支持： 
  * EN AU 
  * EN-CA 
  * EN-GB 
  * EN 

  > [!NOTE]
  > 语言输入 
  >
  > 预生成的收条2.1 版-预览版。1包含一个可选的 request 参数，用于指定来自其他英语市场的回执区域设置。 对于澳大利亚英语 (EN-US) 、加拿大 (EN-CA) 、英国 () 和印度 (EN-US) 中的销售收据，可以指定区域设置以获得改进的结果。 如果未在 v 2.1-preview. 1 中指定区域设置，则模型将默认为 EN-US 模型。

## <a name="customer-scenarios"></a>客户方案  

使用收条 API 提取的数据可用于执行各种任务。 下面是我们的客户使用回执 API 完成的几个示例。 

### <a name="business-expense-reporting"></a>业务支出报表  

通常，归档业务开支需要花费时间从收据的图像中手动输入数据。 使用接收 API，可以使用提取的字段部分自动执行此过程，并快速分析收据。  

由于接收 API 具有简单的 JSON 输出，因此可以通过多种方式使用提取的字段值。 与内部费用应用程序集成，以预填充费用报告。 有关此方案的详细信息，请阅读 Acumatica 如何利用接收 API 来 [使费用报告的过程更少](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)。  

### <a name="auditing-and-accounting"></a>审核和记帐 

还可以使用 "回执 API" 输出在开支报告和补偿过程中的不同时间点对大量支出进行分析。 您可以处理回执，以便对其进行手动审核或快速批准。  

收据输出还适用于一般书籍，以便企业或个人使用。 使用收条 API 将任何原始的接收图像/PDF 数据转换为可操作的数字输出。

### <a name="consumer-behavior"></a>使用者行为 

回执包含有用的数据，可用于分析消费者行为和购物趋势。

