---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: e2c8b4e74933df593dbc023dcb886dff80554a1a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977332"
---
容器的设置是分层的，主计算机上的所有容器都使用共享层次结构。

可以使用以下方法之一指定设置：

* [环境变量](#environment-variable-settings)
* [命令行参数](#command-line-argument-settings)

环境变量值替代命令行参数值，而这些参数值又替代容器映像的默认值。 如果在环境变量和命令行参数中为同一配置设置指定不同的值，则实例化的容器将使用环境变量中的值。

|优先级|正在设置位置|
|--|--|
|1|环境变量| 
|2|命令行|
|3|容器映像默认值|

### <a name="environment-variable-settings"></a>环境变量设置

使用环境变量的优点是：

* 可以配置多个设置。
* 多个容器可以使用相同的设置。

### <a name="command-line-argument-settings"></a>命令行参数设置

使用命令行参数的好处是每个容器都可以使用不同的设置。
