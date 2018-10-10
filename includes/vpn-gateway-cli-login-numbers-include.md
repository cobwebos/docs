---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 82de8eab089e5f666e1a2ce4eab09bfd2895185b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47020075"
---
1. 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 有关登录的详细信息，请参阅 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)。

  ```azurecli
  az login
  ```
2. 如果有多个 Azure 订阅，请列出该帐户的订阅。

  ```azurecli
  az account list --all
  ```
3. 指定要使用的订阅。

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```
