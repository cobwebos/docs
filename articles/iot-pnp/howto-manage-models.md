---
title: 在存储库中管理 IoT 即插即用预览模型*微软文档
description: 如何使用适用于 IoT 门户的 Azure 认证、Azure CLI 和可视化工作室代码管理存储库中的设备功能模型。
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159143"
---
# <a name="manage-models-in-the-repository"></a>管理存储库中的模型

IoT 即插即用预览模型存储库存储设备功能模型和接口。 存储库使模型和接口可由解决方案开发人员发现和消耗。

有三种工具可用于管理存储库：

- 为 IoT 门户认证的 Azure
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>模型存储库

有两种类型的模型存储库用于存储设备功能模型和接口：

- 在[Azure 认证的 IoT 设备目录中](https://aka.ms/iotdevcat)，有一个_公共存储库_存储设备功能模型和设备的接口。 此存储库还存储由 Microsoft 合作伙伴发布的[通用接口](./concepts-common-interfaces.md)[、DCM 和接口](./howto-onboard-portal.md)。 要了解如何认证设备并将其设备功能模型添加到公共存储库，请参阅教程["认证 IoT 即插即用设备](./tutorial-certification-test.md)"。
- 有多个_公司存储库_。 当您[登上 Azure 认证的 IoT 门户时，](./howto-onboard-portal.md)将自动为您的组织创建公司存储库。 您可以在开发和测试期间使用公司存储库来存储设备功能模型和接口。

## <a name="azure-certified-for-iot-portal"></a>为 IoT 门户认证的 Azure

在[IoT](https://preview.catalog.azureiotsolutions.com)的 Azure 认证门户中，可以完成以下任务：

- [完成 IoT 设备的认证过程](./tutorial-certification-test.md)。
- 查找 IoT 即插即用设备功能模型。 您可以使用这些模型[快速构建物联网就绪设备，并将其与解决方案集成](./quickstart-connect-pnp-device-solution-node.md)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 提供用于管理 IoT 即插即用公共和公司模型存储库中的设备功能模型和接口的命令。 有关详细信息，请参阅 Azure [CLI 的"安装和使用 Azure IoT 扩展](./howto-install-pnp-cli.md)"指南。

## <a name="visual-studio-code"></a>Visual Studio Code

在可视化工作室代码中打开**模型存储库**视图。

1. 打开可视化工作室代码，使用**Ctrl_Shift_P**，键入并选择**IoT 即插即用：打开模型存储库**。

1. 您可以选择**打开公共模型存储库**或**打开组织模型存储库**。 对于公司模型存储库，您需要输入模型存储库连接字符串。 可以在 [Azure IoT 认证门户](https://preview.catalog.azureiotsolutions.com)中“公司存储库”的“连接字符串”选项卡上找到此连接字符串。********

1. 新选项卡将打开 **"模型存储库"** 视图。

    使用此视图可以添加、下载和删除设备功能模型和接口。 您可以使用筛选器查找列表中的特定项。

1. 要在公司模型存储库和公共模型存储库之间切换，请使用**Ctrl_Shift_P，** 键入并选择**IoT 即插即用：注销模型存储库**。 然后再次使用**IoT 即插即用：打开模型存储库**命令。

> [!NOTE]
> 在 VS 代码中，公共模型存储库是只读的。 Microsoft 合作伙伴可以在 Azure 认证[IoT 门户](https://preview.catalog.azureiotsolutions.com)中更新公共存储库。

## <a name="next-steps"></a>后续步骤

建议的下一步是学习如何[提交物联网即插即用设备进行认证](tutorial-certification-test.md)。
