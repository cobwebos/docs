---
title: 使用 Azure 数字孪生 CLI
titleSuffix: Azure Digital Twins
description: 请参阅如何开始使用和使用 Azure 数字孪生 CLI。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2c642b2441d1f30c31e707a237732e028f548ac5
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298175"
---
# <a name="use-the-azure-digital-twins-cli"></a>使用 Azure 数字孪生 CLI

除了在 Azure 门户中管理 Azure 数字孪生实例以外，Azure 数字孪生还提供了一个 **命令行接口 (CLI) ** ，可用于对该服务执行大多数主要操作，包括：
* 管理 Azure 数字孪生实例
* 管理模型
* 管理数字孪生
* 管理克隆关系
* 配置终结点
* 管理 [路由](concepts-route-events.md)
* 通过基于角色的访问控制 (RBAC) 配置[安全性](concepts-security.md)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>使用 (部署和验证) 

除了一般管理实例，CLI 也是用于部署和验证的有用工具。
* 使用控制平面命令可重复或自动部署新的实例。
* 数据平面命令可用于快速检查实例中的值，并验证操作是否按预期完成。

## <a name="get-the-extension"></a>获取扩展

Azure 数字孪生命令是 [适用于 Azure CLI 的 Azure IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)的一部分。 您可以在 `az iot` 命令集： [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)中查看这些命令的参考文档。

可以通过以下步骤确保具有最新版本的扩展。 可以在 [Azure Cloud Shell](../cloud-shell/overview.md) 或 [本地 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)中运行这些命令。

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>后续步骤

有关 CLI 命令的替代方法，请参阅如何使用 Api 和 Sdk 管理 Azure 数字孪生实例：
* [*操作说明：使用 Azure 数字孪生 Api 和 Sdk*](how-to-use-apis-sdks.md)
