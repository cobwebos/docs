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
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246732"
---
1. 使用 [az login](/cli/azure/) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 有关登录的详细信息，请参阅 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)。

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
