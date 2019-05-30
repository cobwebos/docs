---
title: 使用 XSLT 映射转换 XML - Azure 逻辑应用 | Microsoft 文档
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中添加用于转换 XML 的 XSLT 映射
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: d0d40ca0ae6ccd4f709d7d94d52764d4affcc215
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244701"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>使用带有 Enterprise Integration Pack 的 Azure 逻辑应用中的映射转换 XML

若要将 XML 数据传输格式用于 Azure 逻辑应用中的企业集成方案之间，逻辑应用可以使用映射或更具体地说，可扩展样式表语言转换 (XSLT) 映射。 映射是一个 XML 文档，描述如何将 XML 文档中的数据转换为另一种格式。 

例如，假设你从使用 YYYMMDD 日期格式的客户定期接收 B2B 订单或发票。 但是，你的组织使用 MMDDYYY 日期格式。 在将订单或发票详细信息存储在客户活动数据库中之前，可以定义并使用一个映射将 YYYMMDD 日期格式转换为 MMDDYYY 格式。

有关集成帐户和映射等项目的限制，请参阅 [Azure 逻辑应用的限制和配置信息](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，用于存储映射以及企业集成与企业到企业 (B2B) 解决方案的其他项目。

* 如果映射引用了外部程序集，必须将该程序集和映射上传到集成帐户。  请确保您[*首先上载您的程序集*](#add-assembly)，然后上传引用程序集的映射。

  如果程序集为 2 MB 或更小，可以直接从 Azure 门户将程序集添加到集成帐户。  但是，如果程序集或映射大于 2 MB 但不超过[程序集或映射的大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)，则可以使用以下选项：

  * 对于程序集，需要一个可在其中上传程序集的 Azure Blob 容器，并规划好该容器的位置。 这样，在将程序集添加到集成帐户时，便可以提供该位置。 
  对于此任务，需要提供以下各项：

    | Item | 描述 |
    |------|-------------|
    | [Azure 存储帐户](../storage/common/storage-account-overview.md) | 在此帐户中创建程序集的 Azure Blob 容器。 了解[如何创建存储帐户](../storage/common/storage-quickstart-create-account.md)。 |
    | Blob 容器 | 可在此容器中上传程序集。 将程序集添加到集成帐户时，也需要此容器的位置。 了解如何[创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 |
    | [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 借助此工具可以更轻松地管理存储帐户和 Blob 容器。 若要使用存储资源管理器，请[下载并安装 Azure 存储资源管理器](https://www.storageexplorer.com/)。 然后，遵循[存储资源管理器入门](../vs-azure-tools-storage-manage-with-storage-explorer.md)中的步骤将存储资源管理器连接到存储帐户。 要了解详细信息，请参阅[快速入门：使用 Azure 存储资源管理器在对象存储中创建 Blob](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)。 <p>或者，在 Azure 门户中找到并选择你的存储帐户。 在存储帐户菜单中选择“存储资源管理器”。  |
    |||

  * 对于映射，目前可以使用 [Azure 逻辑应用 REST API - 映射](https://docs.microsoft.com/rest/api/logic/maps/createorupdate)添加较大的映射。

创建和添加映射时不需要逻辑应用。 但是，若要使用映射，逻辑应用需要链接到存储该映射的集成帐户。 了解[如何将逻辑应用链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。 如果没有逻辑应用，请了解[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>添加引用的程序集

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 若要查找并打开集成帐户，请在 Azure 主菜单中选择“所有服务”。  
   在搜索框中，输入“集成帐户”。 
   选择“集成帐户”。 

   ![查找集成帐户](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. 选择要将程序集添加到的集成帐户，例如：

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. 在集成帐户的“概述”页上的“组件”下，选择“程序集”磁贴。   

   ![选择“程序集”](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. “程序集”页打开后，请选择“添加”。  

   ![选择“添加”](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

根据程序集文件的大小，遵循相应的步骤来上传[不超过 2 MB](#smaller-assembly) 或[大于 2 MB 但不超过 8 MB](#larger-assembly) 的程序集。
有关集成帐户中程序集数量的限制，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)。

> [!NOTE]
> 如果更改您的程序集，还必须更新您的映射映射的更改。

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>添加不超过 2 MB 的程序集

1. 在“添加程序集”下，输入程序集的名称。  将“小文件”保持选定状态。  选择“程序集”框旁边的文件夹图标。  找到并选择要上传的程序集，例如：

   ![上传小型程序集](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   选择程序集后，该程序集的文件名会自动显示在“程序集名称”属性中。 

1. 准备就绪后，选择“确定”  。

   完成程序集文件上传后，该程序集将显示在“程序集”列表中。 

   ![加载的程序集列表](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   在集成帐户的“概述”页上的“组件”下，“程序集”磁贴现在会显示已上传的程序集数目，例如：   

   ![上传的程序集数目](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>添加 2 MB 以上的程序集

若要添加更大的程序集，可将程序集上传到 Azure 存储帐户中的 Azure Blob 容器。 你添加的程序集的步骤因有所不同是否 blob 容器具有公共读取访问权限。 因此，首先请执行以下步骤检查 Blob 容器是否具有公共读取访问权限：[为 Blob 容器设置公共访问级别](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>检查容器访问级别

1. 打开 Azure 存储资源管理器。 在“资源管理器”窗口中展开你的 Azure 订阅（如果尚未展开）。

1. 展开“存储帐户”>“{你的存储帐户}”>“Blob 容器”。    选择你的 Blob 容器。

1. 在 Blob 容器的快捷菜单中，选择“设置公共访问级别”。 

   * 如果 Blob 容器至少具有公共访问权限，请选择“取消”，并执行本页稍后所述的以下步骤：  [上传到具有公共访问权限的容器](#public-access-assemblies)

     ![公共访问权限](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * 如果 Blob 容器没有公共访问权限，请选择“取消”，并执行本页稍后所述的以下步骤：  [上传到没有公共访问权限的容器](#no-public-access-assemblies)

     ![没有公共访问权限](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>上传到具有公共访问权限的容器

1. 将程序集上传到存储帐户。 
   在右侧窗口中，选择“上传”。 

1. 完成上传后，选择上传的程序集。 在工具栏中，选择“复制 URL”以复制程序集的 URL。 

1. 返回 Azure 门户，其中已打开“添加程序集”窗格。  
   输入程序集的名称。 
   选择“大文件(大于 2 MB)”。 

   此时会显示“内容 URI”框而不是“程序集”框。  

1. 在“内容 URI”框中，粘贴程序集的 URL。  
   完成添加程序集。

完成程序集上传后，架构将显示在“程序集”列表中。 
在集成帐户的“概述”页上的“组件”下，“程序集”磁贴现在会显示已上传的程序集数目。   

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>上传到没有公共访问权限的容器

1. 将程序集上传到存储帐户。 
   在右侧窗口中，选择“上传”。 

1. 上传完成后，为程序集生成共享访问签名 (SAS)。 
   在程序集的快捷菜单中，选择“获取共享访问签名”。 

1. 在“共享访问签名”窗格中，选择“生成容器级共享访问签名 URI” > “创建”。    
   生成 SAS URL 后，选择“URL”框旁边的“复制”。  

1. 返回 Azure 门户，其中已打开“添加程序集”窗格。  
   输入程序集的名称。 
   选择“大文件(大于 2 MB)”。 

   此时会显示“内容 URI”框而不是“程序集”框。  

1. 在“内容 URI”框中，粘贴前面生成的 SAS URI。  完成添加程序集。

完成程序集上传后，该程序集将显示在“架构”列表中。  在集成帐户的“概述”页上的“组件”下，“程序集”磁贴现在会显示已上传的程序集数目。   

## <a name="create-maps"></a>创建映射

若要创建可用作映射的 XSLT 文档，可以在 Visual Studio 2015 中使用 [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 创建 BizTalk 集成项目。 在此项目中，可以生成用于直观地在两个 XML 架构文件之间映射项的集成映射文件。 生成此项目后，会获得一个 XSLT 文档。
有关集成帐户中映射数量的限制，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)。 

## <a name="add-maps"></a>添加映射

上传映射引用的所有程序集之后，接下来可以上传映射。

1. 如果尚未登录，请使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。 

1. 如果你的集成帐户尚未打开，请在 Azure 主菜单中选择“所有服务”。  
   在搜索框中，输入“集成帐户”。 
   选择“集成帐户”。 

   ![查找集成帐户](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. 选择要将映射添加到的集成帐户，例如：

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. 在集成帐户的“概述”页上的“组件”下，选择“映射”磁贴。   

   ![选择“映射”](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. “映射”页打开后，请选择“添加”   。

   ![选择“添加”](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>添加不超过 2 MB 的映射

1. 在“添加映射”下，输入映射的名称。  

1. 在“映射类型”下选择类型，例如：  “Liquid”、“XSLT”、“XSLT 2.0”或“XSLT 3.0”。    

1. 将“小文件”保持选定状态。  选择“映射”框旁边的文件夹图标。  找到并选择要上传的映射，例如：

   ![上传映射](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   如果将“名称”属性留空，则选择映射文件后，映射的文件名将自动显示在该属性中。  
   但是，可以使用任何唯一名称。

1. 准备就绪后，选择“确定”  。 
   完成映射文件上传后，该映射将显示在“映射”列表中。 

   ![上传的映射列表](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   在集成帐户的“概述”页上的“组件”下，“映射”磁贴现在会显示已上传的映射数目，例如：   

   ![上传的映射数目](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>添加 2 MB 以上的映射

目前，若要添加较大的映射，需要使用 [Azure 逻辑应用 REST API - 映射](https://docs.microsoft.com/rest/api/logic/maps/createorupdate)。

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>编辑映射

若要更新现有的映射，必须上传一个新的映射文件，其中包含所要做出的更改。 但是，可以先下载现有的映射进行编辑。

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户（如果尚未打开）。

1. 在 Azure 主菜单中，选择“所有服务”  。 在搜索框中，输入“集成帐户”。 选择“集成帐户”。 

1. 选择要在其中更新映射的集成帐户。

1. 在集成帐户的“概述”页上的“组件”下，选择“映射”磁贴。   

1. “映射”页打开后，请选择你的映射。  
   若要先下载并编辑映射，请选择“下载”，然后保存映射。 

1. 准备好上传更新的映射时，请在“映射”页上选择要更新的映射，然后选择“更新”。  

1. 找到并选择要上传的已更新映射。 
   完成映射文件上传后，更新的映射将显示在“映射”列表中。 

## <a name="delete-maps"></a>删除映射

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户（如果尚未打开）。

1. 在 Azure 主菜单中，选择“所有服务”  。 
   在搜索框中，输入“集成帐户”。 
   选择“集成帐户”。 

1. 选择要在其中删除映射的集成帐户。

1. 在集成帐户的“概述”页上的“组件”下，选择“映射”磁贴。   

1. “映射”页打开后，请选择你的映射，然后选择“删除”。  

1. 若要确认删除该映射，请选择“是”。 

## <a name="next-steps"></a>后续步骤

* [了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [详细了解架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [详细了解转换](../logic-apps/logic-apps-enterprise-integration-transform.md)
