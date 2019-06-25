---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173018"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>使用 Azure 文件从浏览器的 Web 应用程序错误 ConditionHeadersNotSupported

当访问通过使应用程序托管在 Azure 文件中的内容使用条件标头，如 web 浏览器，访问将失败，显示 ConditionHeadersNotSupported 错误。

![ConditionHeaderNotSupported 错误](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

尚不支持条件标头。 实施这些应用程序需要请求完整的文件，每次访问该文件。

### <a name="workaround"></a>解决方法

上传新文件时，默认情况下的缓存控制属性是"无缓存"。 若要强制要请求文件的应用程序每次文件的缓存控制属性应从"无缓存"更新为"无缓存、 无存储，必须-在重新"。 这可以使用[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

![存储资源管理器内容缓存修改](media/storage-files-condition-headers/storage-explorer-cache.png)