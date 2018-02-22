---
title: "在 Azure 存储资源管理器中管理 Azure Data Lake Store 资源"
description: "允许用户在 Azure 存储资源管理器中访问和管理 ADLS 数据和资源"
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>使用存储资源管理器（预览版）管理 Azure Data Lake Store 资源

[Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) 是一项服务，用于存储大量的非结构化数据，例如文本数据或二进制数据。 用户可以通过 HTTP 或 HTTPS 从任何位置访问数据。 Azure 存储资源管理器中的 ADLS 允许用户访问和管理 ADLS 数据和资源，以及 Blob 和队列之类的其他 Azure 实体。 现在，用户可以使用同一工具在一个位置管理不同的 Azure 实体。

另一优势是，用户不需拥有订阅权限即可管理 ADLS 数据。 在存储资源管理器中，用户可以将 ADLS 路径附加到“本地和附加”节点，只要其他人授予权限即可。

## <a name="prerequisites"></a>先决条件
若要完成本文中的步骤，需要符合以下先决条件：

*   Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial)。
*   Azure Data Lake Store 帐户。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="installation"></a>安装

安装此处的最新 Azure 存储资源管理器版本：[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。 现在我们支持 Windows、Linux 和 MAC 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 安装 **Azure 存储资源管理器**之后，单击左侧的**插件**图标，如下图中所示。
       
   ![插件图标](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. 选择“添加 Azure 帐户”，然后单击“登录”。

   ![连接到 Azure 订阅](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. 在“Azure 登录”对话框中，选择“登录”，然后输入 Azure 凭据。

    ![登录](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. 从列表中选择你的订阅，然后单击“应用”。

    ![应用](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    资源管理器窗格会更新，并显示所选订阅中的帐户。

    ![帐户列表](./media/data-lake-store-in-storage-explorer/account-list.png)

    已成功地将 **Azure Data Lake Store** 连接到 Azure 订阅。

## <a name="connect-to-data-lake-store"></a>连接到 Data Lake Store
如果需要访问某些资源，但这些资源在订阅中不存在， 而其他人授予了你相关权限，可以获取资源的 URI， 在这种情况下，可以在登录后使用该 URI 连接到 Data Lake Store。 请参阅以下步骤。
1. 打开存储资源管理器（预览版）。
2. 在左窗格中，展开“本地和附加”。
3. 右键单击“Data Lake Store”，然后从上下文菜单中选择“连接到 Data Lake Store...”。

      ![“连接到 Data Lake Store”上下文菜单](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 输入 URI，然后该工具就会导航到刚输入的 URL 的位置。

      ![“连接到 Data Lake Store”上下文对话框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![“连接到 Data Lake Store”操作结果](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>查看 Azure Data Lake Store 帐户的内容
Azure Data Lake Store 帐户的资源包含文件夹和文件。

以下步骤演示了如何在存储资源管理器（预览版）中查看 ADLS 帐户的内容：

1. 打开存储资源管理器（预览版）。
2. 在左窗格中，展开包含要查看的 Azure Data Lake Store 帐户的订阅。
3. 展开“Data Lake Store”。
4. 右键单击要查看的 Azure Data Lake Store 帐户节点，然后从上下文菜单中选择“打开”。 也可双击要打开的 ADLS 帐户。 
5. 主窗格显示 ADLS 帐户的内容。

     ![主窗格](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Azure Data Lake Store 资源管理

可通过以下操作管理 Azure Data Lake Store 资源：
*   在多个 ADL 帐户中导航 ADLS 资源。  
*   使用连接字符串直接连接到 ADLS 并对其进行管理。 
*   通过“本地和附加”项下的 ACL 查看他人共享的 ADLS 资源。
*   执行文件/文件夹 CRUD 操作：支持递归文件夹和多选文件。 
*   将文件夹拖放并添加到进行快速访问的最新位置，这样可镜像桌面文件资源管理器体验。 
*   使用存储资源管理器复制并打开 ADL 超链接，只需单击一下即可。 
*   在右下窗格中显示活动日志，以便查看活动状态。
*   显示文件夹统计信息和文件属性。

## <a name="manage-resources-in-azure-storage-explorer"></a>在 Azure 存储资源管理器中管理资源
创建 Azure Data Lake Store 帐户以后，即可在本地计算机上上传和下载文件夹和文件，以及打开资源。 可以固定到快速访问、新建文件夹、复制 URL、全选 另外还可以执行复制、粘贴、重命名、删除、文件夹统计信息、刷新操作。

以下项目演示了如何在 Azure Data Lake Store 帐户中管理资源。 根据所要执行的任务完成以下步骤：
   * **上传文件**

     1. 在主窗格的工具栏中选择“上传”，然后从下拉菜单中选择“上传文件...”。

        ![“上传文件”菜单](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. 在“选择要上传的文件”对话框中，选择要上传的文件。

        ![“上传文件夹”对话框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. 选择“打开”，开始上传。
   * **上传文件夹**

     1. 在主窗格的工具栏中选择“上传”，然后从下拉菜单中选择“上传文件夹...”。

        ![“上传文件夹”菜单](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. 在“选择要上传的文件夹”对话框中，选择要上传的文件夹。

        ![“上传文件夹”对话框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. 选择“选择文件夹”，开始上传文件夹。

        ![“上传文件夹”对话框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > 可以直接在本地计算机中拖动文件夹和文件来上传。 
       
   * **将文件夹或文件下载到本地计算机**

     1. 选择要下载的文件夹或文件。
     2. 在主窗格的工具栏上，选择“下载”。
     3. 在“选择用于保存已下载文件的文件夹”对话框中，指定用于下载文件夹或文件的位置。 指定要提供给它的名称。
     4. 选择“保存”。
   * **从本地计算机打开文件夹或文件**

     1. 选择要打开的文件夹或文件。
     2. 在主窗格的工具栏中选择“打开”，或者右键单击所选文件夹或文件，然后在上下文菜单中单击“打开”。
     3. 可以在文件下载后使用与文件的基础文件类型相关联的应用程序将其打开。 也可以在主窗格中打开文件夹。

        ![打开文件](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **将文件夹或文件复制到剪贴板**

     1. 选择要复制的文件夹或文件。
     2. 在主窗格的工具栏中选择“复制”，或者右键单击所选文件夹或文件，然后在上下文菜单中单击“复制”。
     3. 在左窗格中导航到另一 ADLS 帐户，然后通过双击在主窗格中查看它。
     4. 在主窗格的工具栏中选择“粘贴”，创建一个副本。 也可在目标中单击上下文菜单的“粘贴”。

        ![复制-粘贴文件夹或文件](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + **不**支持跨存储类型进行复制-粘贴。 可以复制 ADLS 文件夹或文件，然后将其粘贴到另一 ADLS 帐户中。 但是，**不能**在复制 ADLS 文件夹或文件后将其粘贴到 Blob 存储中，反之亦然。 
          > + 复制-粘贴的工作原理是，先将文件夹或文件下载到本地，然后再将其上传到目标。 此工具**不**在后端执行该操作。 大型文件的复制-粘贴速度较慢。 目前正在对高性能的文件复制-移动进行优化。
   * **删除文件夹或文件**

     1. 选择要删除的文件夹或文件。
     2. 在主窗格的工具栏中选择“删除”，或者右键单击所选文件夹或文件，然后在上下文菜单中单击“删除”。
     3. 在确认对话框中选择“是”。

        ![复制-粘贴文件夹或文件](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **固定到快速访问**

     1. 选择要固定的文件夹。
     2. 在主窗格的工具栏中，选择“固定到快速访问”。
     3. 在左窗格中，可以看到所选文件夹已添加到“快速访问”节点。

        ![固定到快速访问](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. 创建快速访问以后，即可轻松地访问资源。
   * **深层链接**
     1. 如果有 URL，则可直接在**文件资源管理器**或浏览器的地址路径中输入该 URL。
     2. 然后，**Storage Explorer.exe** 就会自动启动，导航到刚输入的 URL 的位置。

        ![文件资源管理器中的深层链接](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>后续步骤
* 查看[最新的存储资源管理器（预览版）发行说明和视频](http://www.storageexplorer.com)。
* 了解如何[在 Azure 存储资源管理器中管理 Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)
* 详细了解存储资源管理器[存储资源管理器（预览版）入门](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* Azure Data Lake Store (ADLS) 入门[Azure Data Lake Store 概述](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)
* 观看以下视频，了解[如何在 Azure 存储资源管理器中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)
