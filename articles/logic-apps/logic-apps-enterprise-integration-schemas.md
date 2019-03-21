---
title: 使用架构验证 XML - Azure 逻辑应用 | Microsoft Docs
description: 添加架构以便在带有 Enterprise Integration Pack 的 Azure 逻辑应用中验证 XML 文档
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 3cca995b353b88cc481cbda68df4211a724f7f09
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727308"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中使用架构验证 XML

若要检查文档是否使用有效的 XML 并包含适用于 Azure 逻辑应用中企业集成方案的预定义格式的所需数据，逻辑应用可以使用架构。 架构还可以验证逻辑应用在企业到企业 (B2B) 方案中交换的消息。

有关集成帐户和架构等项目的限制，请参阅 [Azure 逻辑应用的限制和配置信息](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以<a href="https://azure.microsoft.com/free/" target="_blank">注册免费的 Azure 帐户</a>。

* 一个[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，用于存储架构以及企业集成与企业到企业 (B2B) 解决方案的其他项目。 

  如果架构为 [2 MB 或更小](#smaller-schema)，可以直接从 Azure 门户将架构添加到集成帐户。 但是，如果架构大于 2 MB 但不超过[架构大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)，则你可以将架构上传到 Azure 存储帐户。 
  若要将该架构添加到集成帐户，可以从集成帐户链接到存储帐户。 
  若要完成此任务，需要提供以下各项： 

  * [Azure 存储帐户](../storage/common/storage-account-overview.md)，将在其中为架构创建 Blob 容器。 了解[如何创建存储帐户](../storage/common/storage-quickstart-create-account.md)。 

  * 用于存储架构的 Blob 容器。 了解如何[创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 
  稍后将架构添加到集成帐户时，需要容器的内容 URI。

  * [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)，可用于管理存储帐户和 Blob 容器。 
  若要使用存储资源管理器，请选择以下任一选项：
  
    * 在 Azure 门户中找到并选择你的存储帐户。 
    在存储帐户菜单中选择“存储资源管理器”。

    * 对于桌面版本，请[下载并安装 Azure 存储资源管理器](https://www.storageexplorer.com/)。 
    然后，遵循[存储资源管理器入门](../vs-azure-tools-storage-manage-with-storage-explorer.md)中的步骤将存储资源管理器连接到存储帐户。 
    要了解详细信息，请参阅[快速入门：使用 Azure 存储资源管理器在对象存储中创建 Blob](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)。

创建和添加架构时不需要逻辑应用。 但是，若要使用架构，逻辑应用需要链接到存储该架构的集成帐户。 了解[如何将逻辑应用链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。 如果没有逻辑应用，请了解[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-schemas"></a>添加架构

1. 使用 Azure 帐户凭据登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。

1. 若要查找并打开集成帐户，请在 Azure 主菜单中选择“所有服务”。 在搜索框中，输入“集成帐户”。 选择“集成帐户”。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. 选择要将架构添加到的集成帐户，例如：

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. 在集成帐户的“概述”页上的“组件”下，选择“架构”磁贴。

   ![选择“架构”](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. “架构”页打开后，请选择“添加”。

   ![选择“添加”](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

根据架构 (.xsd) 文件的大小，遵循相应的步骤来上传[不超过 2 MB](#smaller-schema) 或[大于 2 MB 但不超过 8 MB](#larger-schema) 的架构。

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>添加不超过 2 MB 的架构

1. 在“添加架构”下，输入架构的名称。 
   将“小文件”保持选定状态。 选择“架构”框旁边的文件夹图标。 找到并选择要上传的架构，例如：

   ![上传小型架构](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. 准备就绪后，选择“确定”。

   完成架构上传后，该架构将显示在“架构”列表中。

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>添加 2 MB 以上的架构

若要添加更大的架构，可将架构上传到 Azure 存储帐户中的 Azure Blob 容器。 将架构添加在步骤因有所不同是否 blob 容器具有公共读取访问权限。 因此，首先请执行以下步骤检查 Blob 容器是否具有公共读取访问权限：[为 Blob 容器设置公共访问级别](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>检查容器访问级别

1. 打开 Azure 存储资源管理器。 在“资源管理器”窗口中展开你的 Azure 订阅（如果尚未展开）。

1. 展开“存储帐户”>“{你的存储帐户}”>“Blob 容器”。 选择你的 Blob 容器。

1. 在 Blob 容器的快捷菜单中，选择“设置公共访问级别”。

   * 如果 Blob 容器至少具有公共访问权限，请选择“取消”，并执行本页稍后所述的以下步骤：[上传到具有公共访问权限的容器](#public-access)

     ![公共访问权限](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * 如果 Blob 容器没有公共访问权限，请选择“取消”，并执行本页稍后所述的以下步骤：[上传到没有公共访问权限的容器](#public-access)

     ![没有公共访问权限](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>上传到具有公共访问权限的容器

1. 将架构上传到存储帐户。 
   在右侧窗口中，选择“上传”。

1. 完成上传后，选择上传的架构。 在工具栏中，选择“复制 URL”以复制架构的 URL。

1. 返回 Azure 门户，其中已打开“添加架构”窗格。 
   输入程序集的名称。 
   选择“大文件(大于 2 MB)”。 

   此时会显示“内容 URI”框而不是“架构”框。

1. 在“内容 URI”框中，粘贴架构的 URL。 
   完成添加架构。

完成架构上传后，该架构将显示在“架构”列表中。 在集成帐户的“概述”页上的“组件”下，“架构”磁贴现在会显示已上传的架构数目。

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>上传到没有公共访问权限的容器

1. 将架构上传到存储帐户。 
   在右侧窗口中，选择“上传”。

1. 上传完成后，为架构生成共享访问签名 (SAS)。 
   在架构的快捷菜单中，选择“获取共享访问签名”。

1. 在“共享访问签名”窗格中，选择“生成容器级共享访问签名 URI” > “创建”。 
   生成 SAS URL 后，选择“URL”框旁边的“复制”。

1. 返回 Azure 门户，其中已打开“添加架构”窗格。 选择“大文件”。

   此时会显示“内容 URI”框而不是“架构”框。

1. 在“内容 URI”框中，粘贴前面生成的 SAS URI。 完成添加架构。

完成架构上传后，该架构将显示在“架构”列表中。 在集成帐户的“概述”页上的“组件”下，“架构”磁贴现在会显示已上传的架构数目。

## <a name="edit-schemas"></a>编辑架构

若要更新现有的架构，必须上传一个新的架构文件，其中包含所要做出的更改。 但是，可以先下载现有的架构进行编辑。

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中，找到并打开你的集成帐户（如果尚未打开）。

1. 在 Azure 主菜单中，选择“所有服务”。 
   在搜索框中，输入“集成帐户”。 
   选择“集成帐户”。

1. 选择要在其中更新架构的集成帐户。

1. 在集成帐户的“概述”页上的“组件”下，选择“架构”磁贴。

1. “架构”页打开后，请选择你的架构。 
   若要先下载并编辑架构，请选择“下载”，然后保存架构。

1. 准备好上传更新的架构时，请在“架构”页上选择要更新的架构，然后选择“更新”。

1. 找到并选择要上传的已更新架构。 
   完成架构文件的上传后，更新的架构将显示在“架构”列表中。

## <a name="delete-schemas"></a>删除架构

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中，找到并打开你的集成帐户（如果尚未打开）。

1. 在 Azure 主菜单中，选择“所有服务”。 
   在搜索框中，输入“集成帐户”。 
   选择“集成帐户”。

1. 选择要在其中删除架构的集成帐户。

1. 在集成帐户的“概述”页上的“组件”下，选择“架构”磁贴。

1. “架构”页打开后，请选择你的架构，然后选择“删除”。

1. 若要确认删除该架构，请选择“是”。

## <a name="next-steps"></a>后续步骤

* [了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md)
* [详细了解映射](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [详细了解转换](../logic-apps/logic-apps-enterprise-integration-transform.md)
