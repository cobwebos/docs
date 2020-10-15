---
title: 查看和管理 Azure Defender 中用于 IoT 的载入传感器
description: 本文介绍如何查看和删除载入传感器，以及如何下载 Azure Defender 中用于 IoT 的载入传感器的新激活文件。
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094263"
---
# <a name="view-and-manage-onboarded-sensors"></a>查看和管理载入传感器

本文介绍如何查看和删除载入传感器，以及如何下载载入传感器的新激活文件。

## <a name="update-sensor-management-mode"></a>更新传感器管理模式

你可能想要更新传感器的管理模式。 执行此操作时，需要从传感器管理页中删除当前传感器，并上传新的激活文件。

- **在云托管模式下工作，而不是在本地托管模式下工作**：使用云托管传感器的激活文件更新本地托管的传感器的激活文件。 重新激活后，传感器检测会显示在传感器和 Azure Defender 的 IoT 门户中。 成功上传重新激活文件后，会将新检测到的警报信息发送到 Azure。

- **在本地托管模式下工作，而不是在云托管模式下工作**：为云托管的传感器更新激活文件，其中包含用于本地管理的传感器的激活文件。 重新激活后，传感器检测信息只显示在传感器中。

- **将传感器关联到新的 Iot 中心**：你可能想要将传感器与新的 iot 中心相关联。 为此，请重新注册传感器，然后上传新的激活文件。

**重新激活传感器：**

1. 导航到 Azure Defender for IoT，" **传感器管理** " 页。

2. 选择要将新激活文件上传到的传感器。

3. 删除它。

4. 请在新模式下，或使用新的 IoT 中心再次在 **载入** 页面上装入传感器。

5. 从 **下载激活文件** 页下载激活文件。

6. 登录到 Azure Defender for IoT 传感器控制台。

7. 在传感器控制台中，选择 " **系统设置** "，然后选择 "重新 **激活**"。

   ![重新激活视图的屏幕截图](media/updates/image14.png)

8. 选择 " **上传** "，然后选择保存的文件。

9. 选择“激活”  。
 
## <a name="delete-sensors"></a>删除传感器

如果删除云托管的传感器，则不会将信息发送到 IoT 中心。

当你不再使用本地托管的传感器时，请将其删除。

**删除传感器：**

1. 导航到 "用于 IoT 的 Azure Defender **传感器" 管理** 页。

2. 选择要删除的传感器。

3. 选择 " **删除传感器**"。

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请转到模块配置的操作方法指南。
> [!div class="nextstepaction"]
> [模块配置操作方法指南](./how-to-agent-configuration.md)
