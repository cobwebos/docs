---
title: "在 Azure 堆栈中应用更新 |Microsoft 文档"
description: "了解如何导入并安装 Microsoft Azure 堆栈集成系统的更新包。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: b00bd606faaffaad30ff6cea3bcf47dc85282f69
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2017
---
# <a name="apply-updates-in-azure-stack"></a>在 Azure 堆栈中应用更新

*适用范围： Azure 堆栈集成系统*

作为 Azure 堆栈操作员，你可以应用 Microsoft 更新为 Azure 堆栈使用更新的包在管理员门户中的磁贴。 您必须下载 Microsoft 更新包，将包文件导入到 Azure 堆栈，然后安装更新包。 

## <a name="download-the-update-package"></a>下载更新包

当 Azure 堆栈的 Microsoft 更新包可用时，到可从 Azure 堆栈的位置下载包，并查看包内容。 更新包通常包含以下文件：

- 自解压*PackageName*.exe 文件。 此文件包含该更新，例如最新累积更新为 Windows Server 的负载。   
- 对应*PackageName*.bin 文件。 这些文件提供压缩，从而提供与关联的负载*PackageName*.exe 文件。 
- Metadata.xml 文件。 此文件包含有关更新，例如发布者、 名称、 先决条件、 大小和支持路径 URL 的基本信息。

## <a name="import-and-install-updates"></a>导入并安装更新

以下过程说明如何导入并在管理员门户中安装更新包。

> [!IMPORTANT]
> 我们强烈建议你通知用户的任何维护操作，并且你在非工作时间期间尽可能多地安排正常的维护时段。 用户工作负荷和门户的操作，则可能会影响维护操作。

1. 在管理员门户中，选择**更多的服务**。 然后，在**数据 + 存储**类别中，选择**存储帐户**。 (或在筛选器框中，开始键入**存储帐户**，然后选择它。)

    ![显示在门户中查找存储帐户的位置](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. 在筛选器框中，键入**更新**，然后选择**updateadminaccount**存储帐户。

    ![演示如何搜索 updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. 在存储帐户的详细信息，在**服务**，选择**Blob**。
 
    ![演示如何获取 blob 的存储帐户](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. 下**Blob 服务**，选择**+ 容器**创建容器。 输入的名称 (例如*更新 1709年*)，然后选择**确定**。
 
     ![演示如何添加存储帐户中的容器](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. 创建容器后，单击容器名称，然后单击**上载**将包文件上载到容器。
 
    ![演示如何上载包文件](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. 下**上载 blob**，单击文件夹图标，浏览到更新包的.exe 文件，然后单击**打开**在文件资源管理器窗口中。
  
7. 下**上载 blob**，单击**上载**。 
 
    ![显示上载每个包文件的位置](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. 重复步骤 6 和 7 适用于*PackageName*.bin 和 Metadata.xml 文件。 
9. 完成后，你可以查看通知 （在门户的右上角的钟形图标）。 通知应表示已完成上载。 
10. 导航回仪表板上的更新磁贴。 该磁贴指出有可用的更新。 单击该磁贴以查看新添加的更新包。
11. 若要安装此更新，选择标记为包**准备**然后右键单击该程序包并选择**立即更新**，或单击**立即更新**顶部附近的操作.
12. 单击安装的更新包时，你可以查看中的状态**更新运行详细信息**区域。 从这里，您也可以单击**下载日志已满**来下载日志文件。
13. 完成更新后，该更新磁贴将显示更新的 Azure 堆栈版本。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 堆栈概述中的更新](azure-stack-updates.md)
- [Azure 堆栈维护策略](azure-stack-servicing-policy.md)
