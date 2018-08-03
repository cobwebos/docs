---
title: 在语言理解 (LUIS) 边界中使用 Node.js 查找 LUIS 区域 | Microsoft Docs
description: 以编程方式查找具有 LUIS 终结点密钥和应用程序 ID 的发布区域。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 6d85e6007b3e85a1b55997541e721ad57c22dddf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111739"
---
# <a name="region-can-be-determined-from-api-call"></a>可通过 API 调用确定区域 
如果具有 LUIS 应用 ID 和 LUIS 订阅 ID，则可查找要用于终结点查询的区域。

> [!NOTE] 
> [LUIS-Samples Github 存储库](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/)中提供了完整的 Node.js 解决方案。

## <a name="luis-endpoint-query-strategy"></a>LUIS 终结点查询策略
每个 LUIS 终结点查询需要：

* 终结点密钥
* 应用 ID
* 区域

如果 LUIS 终结点查询使用了正确的终结点密钥和应用 ID，但区域错误，则响应代码为 401。 401 请求不计入订阅配额。 将此请求转换为可轮询所有区域的策略，以找到正确区域。 正确区域是唯一返回 2xx 状态代码的请求。 

|响应代码|parameters|
|--|--|
|2xx|正确的终结点密钥<br>正确的应用 ID<br>正确的主机区域|
|401|正确的终结点密钥<br>正确的应用 ID<br>不正确的主机区域|

## <a name="nodejs-code-to-find-region"></a>用于查找区域的 Node.js 代码
控制台应用程序使用 LUIS 应用 ID 和终结点密钥，并返回与之关联的所有区域。 目前，终结点密钥由区域创建，因此应仅返回一个区域。

包括 NPM 依赖项：

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

添加常量。 将 `subscriptionKey` 和 `appId` 的变量值替换成自己的值。  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

添加 `searchRegions` 函数以查找区域。 所有不正确的区域都返回 401，此值将被捕获和忽略。

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

调用 `searchRegions` 函数并返回一个区域：

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

运行应用程序时，终端显示对应于应用 ID 的区域。

![显示 LUIS 区域的控制台应用的屏幕截图](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>后续步骤

详细了解 LUIS [区域](luis-reference-regions.md)。
