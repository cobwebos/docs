---
title: 使用 Azure 自动化查看文件内容更改
description: 使用“更改跟踪”的文件内容更改功能查看已更改的文件内容。
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0582505d66bbef3064359fa4047676c4ba60b4e9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37870046"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>查看“更改跟踪”跟踪的文件的内容

使用文件内容跟踪功能，可以查看“更改跟踪”跟踪的文件在某次更改之前和之后的内容。 为了实现这一点，它会在每次更改后将文件内容保存至存储帐户。

## <a name="requirements"></a>要求

* 若要存储文件内容，需要有一个使用资源管理器部署模型的标准存储帐户。 不应使用高级和经典部署模型存储帐户。 有关存储帐户的详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)

* 所使用的存储帐户只能有 1 个与之相连的自动化帐户。

* 在自动化帐户中启用[更改跟踪](automation-change-tracking.md)。

## <a name="enable-file-content-tracking"></a>启用文件内容跟踪

1. 在 Azure 门户中，打开自动化帐户，然后选择“更改跟踪”。
2. 在顶部菜单中选择“编辑设置”。
3. 选择“文件内容”并单击“链接”。 这将打开“添加更改跟踪的内容位置”窗格。

   ![enable](./media/change-tracking-file-contents/enable.png)

4. 选择要用于存储文件内容的订阅和存储帐户。 如果想针对所有现有的已跟踪文件启用文件内容跟踪，请在“上传所有设置的文件内容”处选择“开启”。 可在之后的每个文件路径中更改这一设置。

   ![设置存储帐户](./media/change-tracking-file-contents/storage-account.png)

5. 启用后，会显示存储帐户以及 SAS URI。 SAS URI 会在 365 天以后过期，并且可以通过单击“再生成”按钮重新创建。

   ![列出帐户密钥](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>添加文件

通过以下步骤可以为文件开启更改跟踪：

1. 在“更改跟踪”的“编辑设置”页面上选择“Windows 文件”或“Linux 文件”选项卡，然后单击“添加”

1. 填写文件路径信息，并在“上传所有设置的文件内容”处选择“True”。 此设置只会为该文件路径启用文件内容跟踪。

   ![添加 Linux 文件](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>查看所跟踪文件的内容

1. 只要在文件或该路径中的某个文件中检测到了更改，就会将其显示在门户中。 从更改列表中选择文件更改。 已显示“更改详细信息”窗格。

   ![列出更改内容](./media/change-tracking-file-contents/change-list.png)

1. 在“更改详细信息”页面上，能看到文件信息前后的标准，单击左上角的“查看文件内容更改”可以查看文件的内容。

  ![更改详细信息](./media/change-tracking-file-contents/change-details.png)

1. 该新页面会以对比视图显示文件内容。 也可选择“内联”，以内联视图查看这些更改。

  ![查看文件更改](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>后续步骤

访问有关更改跟踪的教程，详细了解解决方案的用法：

> [!div class="nextstepaction"]
> [排查环境中的更改错误](automation-tutorial-troubleshoot-changes.md)

* 使用 [Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-searches.md)可查看详细的更改跟踪数据。
