---
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 242f601ced4838a1b4e559774c25d05de04ddb77
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "57016328"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>将使用者组添加到 IoT 中心

应用程序使用使用者组从 Azure IoT 中心提取数据。 在本教程中，你将创建一个使用者组，供即将到来的 Azure 服务用来从 IoT 中心读取数据。

要将使用者组添加到 IoT 中心，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中打开 IoT 中心。

2. 在左窗格中单击“内置终结点”，在顶部窗格中选择“事件”，在右窗格的“使用者组”下面输入名称。 更改**默认 TTL** 值并将其恢复为原始值后，单击“保存”。

   ![在 IoT 中心创建使用者组](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
