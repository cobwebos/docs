---
title: 使用 Azure 数字孪生 CLI
titleSuffix: Azure Digital Twins
description: 请参阅如何开始使用和使用 Azure 数字孪生 CLI。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5272babf794529e5e9bd87a3c4a96e6df5758fb8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537456"
---
# <a name="use-the-azure-digital-twins-cli"></a>使用 Azure 数字孪生 CLI

除了在 Azure 门户中管理 Azure 数字孪生实例以外，Azure 数字孪生还提供了一个**命令行接口（CLI）** ，可用于对该服务执行大多数主要操作，包括：
* 管理 Azure 数字孪生实例
* 管理模型
* 管理数字孪生
* 管理克隆关系
* 配置终结点
* 管理[路由](concepts-route-events.md)
* 通过基于角色的访问控制（RBAC）配置[安全性](concepts-security.md)

Azure 数字孪生命令是[适用于 Azure CLI 的 Azure IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)的一部分。 您可以在 `az iot` 命令集： [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)中查看这些命令的参考文档。

## <a name="deploy-and-validate"></a>部署和验证

除了一般管理实例，CLI 也是用于部署和验证的有用工具。
* 使用控制平面命令可重复或自动部署新的实例。
* 数据平面命令可用于快速检查实例中的值，并验证操作是否按预期完成。

## <a name="next-steps"></a>后续步骤

有关 CLI 命令的替代方法，请参阅如何使用 Api 和 Sdk 管理 Azure 数字孪生实例：
* [*操作说明：使用 Azure 数字孪生 Api 和 Sdk*](how-to-use-apis-sdks.md)
