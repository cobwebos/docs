---
title: include 文件
description: include 文件
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008580"
---
第一步是使用 Azure 门户在订阅中创建 IoT 中心。 IoT 中心用于将大量遥测数据从许多设备引入到云中。 然后，该中心会允许一个或多个在云中运行的后端服务读取和处理该遥测数据。

1. 登录到 [Azure 门户](http://portal.azure.com)。

1. 选择“创建资源” > “物联网” > “IoT 中心”。

    ![选择安装 IoT 中心][1]

1. 在“IoT 中心”窗格中，输入 IoT 中心的以下信息：

   * **订阅**：选择需要将其用于创建此 IoT 中心的订阅。
   * **资源组**：创建用来存放 IoT 中心的资源组，或使用现有的资源组。 通过将所有相关资源都放在一个组（例如 **TestResources**）中，可以一起管理它们。 例如，删除资源组会删除包含在该组中的所有资源。 有关详细信息，请参阅[使用资源组管理 Azure 资源][lnk-resource-groups]。
   * **区域**：选择离你的设备最近的位置。
   * **名称**：为你的 IoT 中心创建一个唯一名称。 如果输入的名称可用，会显示一个绿色复选标记。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT 中心基本信息窗口][2]

2. 选择“下一步: 大小和规模”，以便继续创建 IoT 中心。 

3. 选择“定价和缩放层”。 就本文来说，请选择“F1 - 免费”层（前提是此层在订阅上仍然可用）。 有关详细信息，请参阅[定价和缩放层][lnk-pricing]。

   ![IoT 中心大小和规模窗口][3]

4. 选择“查看 + 创建”。

1. 查看 IoT 中心信息，然后单击“创建”。 创建 IoT 中心可能需要数分钟的时间。 可在“通知”窗格中监视进度。


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md