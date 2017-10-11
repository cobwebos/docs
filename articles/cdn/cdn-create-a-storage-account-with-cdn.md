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
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>将 Azure 存储帐户与 Azure CDN 集成
启用 CDN 可以缓存 Azure 存储的内容。 它通过在遍布美国、欧洲、亚洲、澳大利亚和南美洲的众多物理节点上缓存 Blob 和计算实例的静态内容，为开发人员提供一个传送高带宽内容的全球性解决方案。

## <a name="step-1-create-a-storage-account"></a>步骤 1：创建存储帐户
使用以下过程为 Azure 订阅创建新的存储帐户。 通过存储帐户可访问 Azure 存储服务。 存储帐户表示用于访问下述每个 Azure 存储服务组件的最高级别的命名空间：Blob 服务、队列服务、表服务。 有关详细信息，请参阅 [Microsoft Azure 存储简介](../storage/common/storage-introduction.md)。

要创建一个存储帐户，必须是相关订阅的服务管理员或联合管理员。

> [!NOTE]
> 可使用多种方法创建存储帐户，包括 Azure 门户和 Powershell。  本教程使用 Azure 门户。  
> 
> 

**为 Azure 订阅创建存储帐户**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左上角，选择“新建”。 在“新建”对话框中，选择“数据 + 存储”，并单击“存储帐户”。
    
    此时显示“创建存储帐户”边栏选项卡。   

    ![创建存储帐户][create-new-storage-account]  

3. 在“名称”字段中，键入子域名称。 此条目可包含 3-24 个小写字母和数字。
   
    此值将成为用于对订阅的 Blob、队列或表资源进行寻址的 URI 中的主机名。 若要对 BLOB 服务中的容器资源进行寻址，需使用以下格式的 URI，其中 *&lt;StorageAccountLabel&gt;* 是指在“输入 URL”中键入的值：
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **重要提示：**该 URL 标签构成存储帐户 URI 的子域，必须在 Azure 中的所有托管服务中独一无二。
   
    此值还在门户中用作此存储帐户的名称，或在通过编程方式访问此帐户时使用。
4. 保留**部署模型**、**帐户类型**、**性能**和**复制**的默认值。 
5. 选择将与存储帐户一起使用的**订阅**。
6. 选择或创建“资源组” 。  有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md#resource-groups)。
7. 选择存储帐户的位置。
8. 单击“创建” 。 创建存储帐户的过程可能需要几分钟时间完成。

## <a name="step-2-enable-cdn-for-the-storage-account"></a>步骤 2：为存储帐户启用 CDN

利用最新的集成，现无需离开存储门户扩展即可为存储帐户启用 CDN。 

1. 选择存储帐户，搜索“CDN”或从左侧导航菜单中向下滚动，并单击“Azure CDN”。
    
    随即显示“Azure CDN”边栏选项卡。

    ![CDN 启用导航][cdn-enable-navigation]
    
2. 输入所需信息，创建新的终结点
    - CDN 配置文件：可新建配置文件或使用现有配置文件。
    - 定价层：如果创建新的 CDN 配置文件，则只需选择定价层。
    - CDN 终结点名称：根据选择输入终结点名称。

    > [!TIP]
    > 默认情况下，创建的 CDN 终结点将使用存储帐户的主机名作为源。

    ![创建新的 CDN 终结点][cdn-new-endpoint-creation]

3. 创建后，新终结点会显示在上方的终结点列表中。

    ![CDN 存储新终结点][cdn-storage-new-endpoint]

> [!NOTE]
> 还可转到 Azure CDN 扩展来启用CDN。[教程](#Tutorial-cdn-create-profile)。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>步骤 3：启用其他 CDN 功能

从存储帐户的“Azure CDN”边栏选项卡中，单击列表中的 CDN 终结点，打开“CDN 配置”边栏选项卡。 可为交付内容启用其他 CDN 功能，例如压缩、查询字符串和地区筛选。 还可将自定义域映射添加到 CDN 终结点并启用自定义域 HTTPS。
    
![CDN 存储 CDN 配置][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>步骤 4：访问 CDN 内容
若要访问 CDN 上的缓存内容，请使用门户中提供的 CDN URL。 缓存 Blob 的地址将如下所示：

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> 一旦启用对存储帐户的 CDN 访问，所有公开可用的对象即有资格获得 CDN 边缘缓存。 如果修改一个当前在 CDN 中缓存的对象，则只有 CDN 在缓存内容生存时间到期时刷新了对象的内容后，才能通过 CDN 访问新内容。
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>步骤 5：从 CDN 删除内容
如果不再想在 Azure 内容交付网络 (CDN) 中缓存对象，则可执行下列步骤之一：

* 可将容器设为专用的而不是公用的。 有关详细信息，请参阅 [管理对容器和 Blob 的匿名读取访问](../storage/blobs/storage-manage-access-to-resources.md) 。
* 可使用管理门户禁用或删除 CDN 终结点。
* 可将托管服务修改为不再响应此对象的请求。

已在 CDN 中缓存的对象将保持缓存状态，直至对象的生存时间到期，或直至清除终结点为止。 当生存时间到期时，CDN 将查看 CDN 终结点是否仍有效，且是否仍可对该对象进行匿名访问。 如果不能，则不再对该对象进行缓存。

## <a name="additional-resources"></a>其他资源
* [如何将 CDN 内容映射到自定义域](cdn-map-content-to-custom-domain.md)
* [为自定义域启用 HTTPS](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
