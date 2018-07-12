---
title: include 文件
description: include 文件
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366044"
---
<a name="paths"></a>
## <a name="paths"></a>路径

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>检测所请求的时序数据点的异常点
```
POST /anomalydetection
```


#### <a name="parameters"></a>parameters

|Type|名称|说明|架构|
|---|---|---|---|
|**正文**|**body**  <br>（必需）|时序数据点和周期（如需）。|[请求](#request)|


#### <a name="responses"></a>响应

|HTTP 代码|说明|架构|
|---|---|---|
|**200**|成功的操作。|< [响应](#response) > 数组|
|**400**|无法分析 JSON 请求。|无内容|
|**403**|服务器不接受所提供的证书。|无内容|
|**405**|不允许使用该方法。|无内容|
|**500**|内部服务器错误。|无内容|


#### <a name="consumes"></a>使用

* `application/json`


#### <a name="produces"></a>生成

* `application/json`


#### <a name="tags"></a>标记

* AnomalyDetection



