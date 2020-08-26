---
author: baanders
description: 介绍如何验证 Azure 数字孪生模型的文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985864"
---
> [!TIP]
> 创建模型后，建议在将模型上传到 Azure 数字孪生实例之前，先对其进行验证。

有一种与语言无关的示例可用于验证模型文档，以确保 DTDL 是正确的。 其位置为： [**DTDL 验证器示例**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)。

DTDL 验证器示例建立在一个 .NET DTDL 分析器库上，该库在 NuGet 上作为客户端库提供： [**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 你还可以直接使用库来设计自己的验证解决方案。 使用分析器库时，请确保使用与 Azure 数字孪生运行的版本兼容的版本。 预览期间，此版本为*3.7.0*。

可以在[*操作方法：分析和验证模型*](../articles/digital-twins/how-to-parse-models.md)中了解有关验证程序示例和分析器库的详细信息，包括用法示例。