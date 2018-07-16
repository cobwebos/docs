---
title: 在 Azure Stack 中应用更新 | Microsoft Docs
description: 了解如何为 Azure Stack 集成系统导入并安装 Microsoft 更新包。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: da8261d27ae7fad3c5ff30e4e1cce3f1bca2b70a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035327"
---
# <a name="apply-updates-in-azure-stack"></a>在 Azure Stack 中应用更新

*适用于：Azure Stack 集成系统*

Azure Stack 操作员，可以将应用 Microsoft 或 OEM 更新包适用于 Azure Stack 通过使用更新磁贴在管理员门户中。 你必须下载更新包、 包文件导入到 Azure Stack，然后安装更新包。 

## <a name="download-the-update-package"></a>下载更新包

适用于 Azure Stack 的 Microsoft 或 OEM 更新包可用时，到可从 Azure Stack 访问的位置下载包并查看包的内容。 更新包通常包含以下文件：

- 自解压 *PackageName*.exe 文件。 此文件包含更新的有效负载，例如，Windows Server 的最新累积更新。   
- 相应的 *PackageName*.bin 文件。 这些文件为与 *PackageName*.exe 文件关联的有效负载提供压缩。 
- Metadata.xml 文件。 此文件包含有关更新的基本信息，例如发布者、名称、先决条件、大小和支持路径 URL。

## <a name="import-and-install-updates"></a>导入并安装更新

以下过程说明如何在管理员门户中导入并安装更新包。

> [!IMPORTANT]
> 我们强烈建议你向用户通知任何维护操作，并尽可能将正常维护时段安排在非工作时间。 维护操作可能会同时影响用户工作负荷和门户操作。

1. 在管理员门户中，选择“更多服务”。 然后，在“数据 + 存储”类别下，选择“存储帐户”。 （或者，在筛选框中开始键入“存储帐户”，然后选择它。）

    ![显示在门户中查找存储帐户的位置](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. 在筛选框中，键入“更新”，然后选择 **updateadminaccount** 存储帐户。

    ![显示如何搜索 updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. 在存储帐户详细信息中，在“服务”下，选择 **Blob**。
 
    ![显示如何转到存储帐户的 Blob](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. 在“Blob 服务”下，选择“+ 容器”创建容器。 输入名称（例如 *Update-1709*），然后选择“确定”。
 
     ![显示如何在存储帐户中添加容器](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. 创建容器后，单击容器名称，然后单击“上传”将包文件上传到容器。
 
    ![显示如何上传包文件](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. 在“上传 blob”下，单击文件夹图标，浏览到更新包的 .exe 文件，然后在文件资源管理器窗口中单击“打开”。
  
7. 在“上传 blob”下，单击“上传”。 
 
    ![显示上传每个包文件的位置](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. 对于 *PackageName*.bin 和 Metadata.xml 文件，重复步骤 6 和步骤 7。 不要导入 Supplemental Notice.txt 文件（如果已包含）。
9. 完成后，可以查看通知（在门户右上角的钟形图标）。 通知应指示已完成上传。 
10. 导航回仪表板上的“更新”磁贴。 该磁贴应指示有可用更新。 单击该磁贴可查看新添加的更新包。
11. 若要安装更新，请选择标记为“就绪”的包，然后右键单击该包并选择“立即更新”，或者单击顶部附近的“立即更新”操作。
12. 单击正在安装的更新包时，可以在“更新运行详细信息”区域中查看状态。 还可以在此处单击“下载完整日志”以下载日志文件。
13. 完成更新后，“更新”磁贴将显示更新后的 Azure Stack 版本。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中管理更新概述](azure-stack-updates.md)
- [Azure Stack 服务策略](azure-stack-servicing-policy.md)
