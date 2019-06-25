---
title: include 文件
description: include 文件
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172942"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>部署具有市场条款的映像

Azure 市场中的某些 VM 映像具有附加许可条款和购买条款，你必须接受这些条款，然后才能以编程方式部署这些映像。  

若要从此类映像部署 VM，需要同时接受映像的条款并启用编程部署。 只需对每个订阅执行一次此操作。 此后，每次以编程方式从映像部署 VM 时，还需要指定“购买计划”  参数。

以下部分介绍如何执行这些操作：

* 了解市场映像是否具有附加许可条款 
* 以编程方式接受条款
* 以编程方式部署 VM 时提供购买计划参数

