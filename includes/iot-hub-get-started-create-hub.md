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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "34371229"
---
## <a name="create-an-iot-hub"></a>创建 IoT 中心
为模拟设备应用创建要连接到的 IoT 中心。 以下步骤说明如何使用 Azure 门户来完成此任务。

1. 登录到 [Azure 门户][lnk-portal]。

1. 选择“创建资源” > “物联网” > “IoT 中心”。
   
    ![Azure 门户跳转栏][1]

1. 在“IoT 中心”窗格中，输入 IoT 中心的以下信息：

   * **订阅**：选择需要将其用于创建此 IoT 中心的订阅。

   * **资源组**：创建用于托管 IoT 中心的资源组，或使用现有的资源组。 有关详细信息，请参阅[使用资源组管理 Azure 资源][lnk-resource-groups]。

   * **区域**：选择最近的位置。

   * **名称**：创建 IoT 中心的名称。 如果输入的名称可用，会显示一个绿色复选标记。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT 中心基本信息窗口][2]

2. 选择“下一步: 大小和规模”，以便继续创建 IoT 中心。 

3. 选择“定价和缩放层”。 就本文来说，请选择“F1 - 免费”层（前提是此层在订阅上仍然可用）。 有关详细信息，请参阅[定价和缩放层][lnk-pricing]。

   ![IoT 中心大小和规模窗口][3]

4. 选择“查看 + 创建”。

1. 查看 IoT 中心信息，然后单击“创建”。 创建 IoT 中心可能需要数分钟的时间。 可在“通知”窗格中监视进度。

1. 新的 IoT 中心就绪以后，请在 Azure 门户中单击其磁贴，打开其属性窗口。 创建 IoT 中心以后，即可找到将设备和应用程序连接到 IoT 中心时需要使用的重要信息。 单击“共享访问策略”。
   
1. 在“共享访问策略”中，选择 **iothubowner** 策略。 复制 IoT 中心**连接字符串 ---主密钥**供以后使用。 有关详细信息，请参阅“IoT 中心开发人员指南”中的[访问控制][lnk-access-control]。
   
    ![共享访问策略][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
