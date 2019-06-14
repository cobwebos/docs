---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124319"
---
### <a name="run-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要使用公开端口运行多个容器，请确保在运行每个容器时使用不同的公开端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

可以让此容器和其他 Azure 认知服务容器一起运行在该主机上。 此外，还可以让同一认知服务容器的多个容器一起运行。
