---
title: 在语言理解 (LUIS) 边界中使用 C# 查找 LUIS 区域 | Microsoft Docs
description: 以编程方式查找具有 LUIS 订阅密钥和应用程序 ID 的发布区域。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2018
ms.locfileid: "35366923"
---
# <a name="region-can-be-determined-from-api-call"></a>可通过 API 调用确定区域 
如果具有 LUIS 应用 ID 和 LUIS 订阅 ID，则可查找要用于终结点查询的区域。

> [!NOTE] 
> [LUIS-Samples Github 存储库](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/)中提供了完整的 C# 解决方案。

## <a name="luis-endpoint-query-strategy"></a>LUIS 终结点查询策略
每个 LUIS 终结点查询需要：

* 订阅密钥
* 应用 ID
* 区域

如果 LUIS 终结点查询使用了正确的订阅密钥和应用 ID，但区域错误，则响应代码为 401。 401 请求不计入订阅配额。 将此请求转换为可轮询所有区域的策略，以找到正确区域。 正确区域是唯一返回 2xx 状态代码的请求。 

|响应代码|parameters|
|--|--|
|2xx|正确的订阅密钥<br>正确的应用 ID<br>正确的主机区域|
|401|正确的订阅密钥<br>正确的应用 ID<br>不正确的主机区域|

## <a name="c-class-code-to-find-region"></a>用于查找区域的 C# 类代码
控制台应用程序使用 LUIS 应用 ID 和订阅密钥，并返回与之关联的所有区域。 目前，订阅密钥由区域创建，因此应仅返回一个区域。

包括 .Net 库依赖项：

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

添加生成的此自定义 LUIS 类，用于查找区域。 将 `luisAppId` 和 `luisSubscriptionKey` 的变量值替换成自己的值。 返回 401 的所有区域都将写入调试控制台。 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

下面是在控制台应用程序的 Main 方法中调用自定义 LUIS 类的示例：

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

运行应用程序时，控制台显示对应于应用 ID 和订阅密钥的区域。

![显示 LUIS 区域的控制台应用的屏幕截图](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>后续步骤

详细了解 LUIS [区域](luis-reference-regions.md)。
