---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050414"
---
## <a name="deployment-customization"></a>部署自定义

部署过程假设推送的 .zip 文件包含随时可运行的应用。 默认情况下，不会运行自定义。 若要启用通过持续集成获取的同一生成进程，请将以下内容添加到应用程序设置：

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

使用 .zip 推送部署时，此设置默认为“false”  。 持续集成部署的设置默认为“true”  。 设置为“true”时，在部署期间将使用与部署相关的设置  。 可以将这些设置配置为应用设置或在位于 .zip 文件根目录中的 .deployment 配置文件中进行配置。 有关详细信息，请参阅部署参考中的 [Repository and deployment-related settings](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)（存储库和与部署相关的设置）。