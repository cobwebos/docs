---
title: include 文件
description: include 文件
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173508"
---
必须满足以下要求才能完成本教程：

**Azure CLI**：必须在本地计算机上安装 Azure CLI 2.0.29 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

**Docker**：本教程假设读者基本了解核心 Docker 概念，如容器、容器映像和基本的 `docker` 命令。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述][docker-get-started]。

**Docker 引擎**：若要完成本教程，需在本地安装 Docker 引擎。 Docker 提供用于在 [macOS][docker-mac]、[Windows][docker-windows] 和 [Linux][docker-linux] 上配置 Docker 环境的包。

> [!IMPORTANT]
> 由于 Azure Cloud Shell 不包含 Docker 守护程序，因此，必须在本地计算机上安装 Azure CLI 和 Docker 引擎才能完成本教程。   不能在本教程中使用 Azure Cloud Shell。

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
