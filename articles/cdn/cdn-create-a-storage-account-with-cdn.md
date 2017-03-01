---
title: "将 Azure 存储帐户与 Azure CDN 集成 | Microsoft Docs"
description: "了解如何使用 Azure 内容交付网络 (CDN) 通过缓存 Azure 存储中的 blob 来交付高带宽内容。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 06bd0112eab46f3347dfb039a99641a37c2b0197
ms.openlocfilehash: 3a0db75612531ebf947c011e9e4545f7d9590e20


---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>将 Azure 存储帐户与 Azure CDN 集成
启用 CDN 可以缓存 Azure 存储空间的内容。 它通过在遍布美国、欧洲、亚洲、澳大利亚和南美洲的众多物理节点上缓存 Blob 和计算实例的静态内容，为开发人员提供一个传送高带宽内容的全球性解决方案。

## <a name="step-1-create-a-storage-account"></a>步骤 1：创建存储帐户
使用以下过程为 Azure 订阅创建新的存储帐户。 通过存储帐户可访问 Azure 存储服务。 存储帐户表示用于访问下述每个 Azure 存储服务组件的最高级别的命名空间：Blob 服务、队列服务、表服务。 有关详细信息，请参阅 [Microsoft Azure 存储简介](../storage/storage-introduction.md)。

若要创建一个存储帐户，你必须是相关订阅的服务管理员或联合管理员。

> [!NOTE]
> 可使用多种方法创建存储帐户，包括 Azure 门户和 Powershell。  本教程使用 Azure 门户。  
> 
> 

**为 Azure 订阅创建存储帐户**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左上角，选择“新建”。 在“新建”对话框中，选择“数据 + 存储”，然后单击“存储帐户”。
   
   此时将显示“创建存储帐户”边栏选项卡。
   
   ![创建存储帐户][create-new-storage-account]
3. 在“名称”字段中，键入子域名称。 此条目可包含 3-24 个小写字母和数字。
   
    此值将成为用于对订阅的 Blob、队列或表资源进行寻址的 URI 中的主机名。 若要对 Blob 服务中的容器资源进行寻址，需使用以下格式的 URI，其中 *&lt;StorageAccountLabel&gt;* 是指在“输入 URL”中键入的值：
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **重要提示：**该 URL 标签构成存储帐户 URI 的子域，必须在 Azure 中的所有托管服务中独一无二。
   
    此值还在门户中用作此存储帐户的名称，或在通过编程方式访问此帐户时使用。
4. 保留**部署模型**、**帐户类型**、**性能**和**复制**的默认值。 
5. 选择将与存储帐户一起使用的**订阅**。
6. 选择或创建“资源组” 。  有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md#resource-groups)。
7. 选择存储帐户的位置。
8. 单击“创建” 。 创建存储帐户的过程可能需要几分钟时间完成。

## <a name="step-2-create-a-new-cdn-profile"></a>步骤 2：创建新的 CDN 配置文件
CDN 配置文件是 CDN 终结点的集合。  每个配置文件包含一个或多个 CDN 终结点。  你可能希望通过 Internet 域、Web 应用程序或其他条件来使用多个配置文件以组织 CDN 终结点。

> [!TIP]
> 如果已有要用于此教程的 CDN 配置文件，请转至[步骤 3](#step-3-create-a-new-cdn-endpoint)。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>步骤 3：创建新的 CDN 终结点
**为存储帐户创建新的 CDN 终结点**

1. 在 [Azure 管理门户](https://portal.azure.com)中，导航到 CDN 配置文件。  可能在先前步骤中将其固定到了仪表板。  如果不是，则可单击“浏览”、“CDN 配置文件”，然后单击计划向其添加终结点的配置文件。
   
    将出现 CDN 配置文件边栏选项卡。
   
    ![CDN 配置文件][cdn-profile-settings]
2. 单击“添加终结点”  按钮。
   
    ![“添加终结点”按钮][cdn-new-endpoint-button]
   
    将出现“添加终结点”  边栏选项卡。
   
    ![“添加终结点”边栏选项卡][cdn-add-endpoint]
3. 为该 CDN 终结点输入 **名称** 。  此名称可用于访问在域 `<endpointname>.azureedge.net`中缓存的资源。
4. 在“源类型”下拉列表中，选择“存储”。  
5. 在“源主机名”下拉列表中，选择存储帐户。
6. 保留**源路径**、**源主机头**和**协议/源端口**的默认值。  必须至少指定一种协议（HTTP 或 HTTPS）。
   
   > [!NOTE]
   > 此配置启用在 CDN 中进行缓存的存储帐户中的所有公开可见容器。  如果希望将范围限制为单个容器，请使用**源路径**。  请注意，必须将容器可见性设置为公开。
   > 
   > 
7. 单击“创建”  按钮创建新的终结点。
8. 终结点创建后，将出现在配置文件的终结点列表中。 该列表视图显示用于访问缓存内容的 URL 以及原始域。
   
    ![CDN 终结点][cdn-endpoint-success]
   
   > [!NOTE]
   > 终结点不会立即可用。  通过 CDN 网络传播注册可能需要长达 90 分钟。 尝试立即使用 CDN 域名的用户可能会收到状态代码 404，直到可通过 CDN 访问该内容。
   > 
   > 

## <a name="step-4-access-cdn-content"></a>步骤 4：访问 CDN 内容
若要访问 CDN 上的缓存内容，请使用门户中提供的 CDN URL。 缓存 Blob 的地址将如下所示：

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> 一旦启用对存储帐户或托管服务的 CDN 访问，所有公开可用的对象将有资格获得 CDN 边缘缓存。 如果你修改一个当前在 CDN 中缓存的对象，则只有 CDN 在缓存内容生存时间到期时刷新了对象的内容后，才能通过 CDN 访问新内容。
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>步骤 5：从 CDN 删除内容
如果你不再想在 Azure 内容交付网络 (CDN) 中缓存对象，则可执行下列步骤之一：

* 您可将容器设为专用的而不是公用的。 有关详细信息，请参阅 [管理对容器和 Blob 的匿名读取访问](../storage/storage-manage-access-to-resources.md) 。
* 你可使用管理门户禁用或删除 CDN 终结点。
* 你可将托管服务修改为不再响应此对象的请求。

已在 CDN 中缓存的对象将保持缓存状态，直至对象的生存时间到期，或直至清除终结点为止。 当生存时间到期时，CDN 将查看 CDN 终结点是否仍有效，且是否仍可对该对象进行匿名访问。 如果不能，则不再对该对象进行缓存。

## <a name="additional-resources"></a>其他资源
* [如何将 CDN 内容映射到自定义域](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png



<!--HONumber=Jan17_HO4-->


