---
title: 在存储库中管理 IoT 即插即用预览模型 |Microsoft Docs "
description: 如何使用 Azure IoT 认证门户、Azure CLI 和 Visual Studio code 管理存储库中的设备功能模型。
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: 1b71d8bd0f0417c7dc408c580a1c73ac654743ce
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932859"
---
# <a name="manage-models-in-the-repository"></a>管理存储库中的模型

IoT 即插即用预览模型存储库存储设备功能模型和接口。 此存储库使解决方案开发人员可发现和使用模型和接口。

有三种工具可用于管理存储库：

- Azure IoT 认证门户
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>模型存储库

有两种类型的模型存储库用于存储设备功能模型和接口：

- 有一个_公共存储库_，它在[Azure IoT 认证设备目录](https://aka.ms/iotdevcat)中存储设备的设备功能模型和接口。 此存储库还存储[由 Microsoft 合作伙伴发布的](./howto-onboard-portal.md)[通用接口](./concepts-common-interfaces.md)和 DCMs 和接口。 若要了解如何认证设备并将其设备功能模型添加到公共存储库，请参阅教程[验证 IoT 即插即用设备](./tutorial-certification-test.md)。
- 有多个_公司存储库_。 当你加入[到 Azure IoT 认证门户](./howto-onboard-portal.md)时，系统会自动为你的组织创建一个公司存储库。 你可以在开发和测试过程中使用公司存储库来存储设备功能模型和接口。

## <a name="azure-certified-for-iot-portal"></a>Azure IoT 认证门户

在[Azure IoT 认证门户](https://preview.catalog.azureiotsolutions.com)中，可以完成以下任务：

- [完成 IoT 设备的认证过程](./tutorial-certification-test.md)。
- 查找 IoT 即插即用设备功能型号。 你可以使用这些模型[快速构建 IoT ready 设备，并将它们与解决方案集成](./quickstart-connect-pnp-device-solution.md)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 提供了用于管理 IoT 即插即用公用和公司模型存储库中的设备功能模型和接口的命令。 有关详细信息，请参阅[安装和使用适用于 Azure CLI](./howto-install-pnp-cli.md)操作方法指南的 Azure IoT 扩展。

## <a name="visual-studio-code"></a>Visual Studio Code

若要在 Visual Studio Code 中打开**模型存储库**视图。

1. 打开 Visual Studio Code，使用**Ctrl + Shift + P**，键入并选择**IoT 即插即用：打开模型存储**库。

1. 您可以选择**打开公共模型存储库**或**打开组织模型存储库**。 对于公司模型存储库，需要输入模型存储库连接字符串。 可以在 [Azure IoT 认证门户](https://preview.catalog.azureiotsolutions.com)中“公司存储库”的“连接字符串”选项卡上找到此连接字符串。

1. 新选项卡将打开**模型存储库**视图。

    使用此视图可以添加、下载和删除设备功能模型和接口。 您可以使用筛选器来查找列表中的特定项。

1. 若要在公司模型存储库和公共模型存储库之间切换，请按**Ctrl + Shift + P**， **键入并选择 IoT 即插即用：注销模型存储库**。 然后使用**IoT 即插即用：再次打开模型**存储库命令。

> [!NOTE]
> 在 VS Code 中，公共模型存储库是只读的。 Microsoft 合作伙伴可以在[Azure IoT 认证门户](https://preview.catalog.azureiotsolutions.com)中更新公共存储库。

## <a name="next-steps"></a>后续步骤

建议的下一步是了解如何[提交 IoT 即插即用设备进行认证](tutorial-certification-test.md)。
