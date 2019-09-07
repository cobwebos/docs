---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70737482"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>从浏览器访问使用 Azure 文件存储的 Web 应用程序时出现错误 ConditionHeadersNotSupported

通过使用条件标头的应用程序（如 web 浏览器）访问 Azure 文件中托管的内容时，会发生 ConditionHeadersNotSupported 错误。 错误指出不支持条件标头。

![Azure 文件条件标头错误](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

尚不支持条件标头。 实现它们的应用程序将需要在每次访问文件时请求完整的文件。

### <a name="workaround"></a>解决方法

上传新文件时，cache-control 属性默认为“no-cache”。 若要强制应用程序每次请求文件，需要将文件的 cache-control 属性从“no-cache”更新为“no-cache, no-store, must-revalidate”。 这可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)来实现。

![Azure 文件条件标头的存储资源管理器内容缓存修改](media/storage-files-condition-headers/storage-explorer-cache.png)