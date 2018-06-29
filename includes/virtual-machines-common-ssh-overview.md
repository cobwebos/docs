---
title: include 文件
description: include 文件
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 5e199771c39ba2fbdeabbd04ed4081a9cd3ea117
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2018
ms.locfileid: "36964512"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 和密钥概述

SSH 是一种加密的连接协议，利用该协议可以通过未受保护的连接进行安全登录。 它是在 Azure 中托管的 Linux VM 的默认连接协议。 虽然 SSH 本身提供加密连接，但是将密码用于 SSH 连接仍使 VM 易受到强力破解攻击或猜测密码。 使用 SSH 连接到 VM 的更安全且首选的方法是使用公钥-私钥对，也称为 SSH 密钥。 

* *公钥*放置在 Linux VM 上或者要对其使用公钥加密的任何其他服务中。

* 私钥是在建立 SSH 连接时向 Linux VM 呈现的内容，以验证身份。 请保护好私钥， 不要透露给其他人。

根据组织的安全策略，可重复使用单个公钥-私钥对来访问多个 Azure VM 和服务。 无需对要访问的每个 VM 或服务使用单独的密钥对。 

公钥可与任何人共享；但只有你（或本地安全基础结构）才拥有私钥。