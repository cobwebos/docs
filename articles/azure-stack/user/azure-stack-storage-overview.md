---
title: "Azure 堆栈存储空间简介"
description: "了解有关 Azure 堆栈存储空间"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 8777aa486a627cf8b2d8ba443e115638354d10da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-storage"></a>Azure 堆栈存储空间简介

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

## <a name="overview"></a>概述
Azure 堆栈存储是一组云存储服务包括 Blob、 表和队列这些队列是与 Azure 存储空间服务一致。

## <a name="azure-stack-storage-services"></a>Azure 堆栈存储服务
Azure 堆栈存储提供了以下三个服务：

* **Blob 存储** 

    Blob 存储存储非结构化的对象数据。 Blob 可以是任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。
* **表存储** 

    表存储可存储结构化数据集。 表存储是一个 NoSQL“键-属性”数据存储，可以用于实现快速开发以及快速访问大量数据。
* **队列存储** 

    队列存储为云服务的各个组件之间的工作流处理和通信提供可靠的消息传送。

Azure 堆栈存储帐户是一种安全帐户，使您可以访问 Azure 堆栈存储中的服务。 存储帐户为存储资源提供唯一的命名空间。 下图显示了存储帐户中的 Azure 堆栈存储资源之间的关系：

![Azure 堆栈存储概述](media/azure-stack-storage-overview/AzureStackStorageOverview.png)


### <a name="blob-storage"></a>Blob 存储

要在云中存储有大量非结构化的对象数据的用户，Blob 存储提供一个有效且可伸缩的解决方案。 可以使用 Blob 存储来存储如下内容：

* 文档
* 社交数据，例如照片、视频、音乐和博客
* 文件、计算机、数据库和设备的备份
* Web 应用程序的图像和文本
* 云应用程序的配置数据
* 大数据，例如日志和其他大型数据集

每个 Blob 都组织到一个容器中。 容器还提供了一种有用的方式来向对象组分配安全策略。 存储帐户可以包含任意数量的容器，和一个容器可以包含任意数目的 blob，最大限制为存储帐户。

Blob 存储提供了三种类型的 blob: 
* **块 blob** 

    块 Blob 进行了相应的优化来流化和存储云对象，并且是用于存储文档、介质文件和备份等对象的不错选择。
* **追加 blob** 

    追加 Blob 类似于块 Blob，但针对追加追加操作进行了优化。 追加 Blob 仅可以通过将新的块添加到末尾来进行更新。 对于需要新数据只能写入到 Blob 结尾的情况，例如日志记录，追加 Blob 是一个不错的选择 。
* **页 blob** 

    页 blob 进行了优化来表示 IaaS 磁盘和支持随机写入即达 1 TB 的大小。 Azure 堆栈虚拟机连接的 IaaS 磁盘是作为页 blob 存储的 VHD。


### <a name="table-storage"></a>表存储
与前几代的必需软件相比，现代应用程序通常要求数据存储具有更高的可伸缩性和灵活性。 表存储提供了具有高可用性且可大规模伸缩的存储，因此应用程序可以自动伸缩以满足用户需求。 表存储是 Microsoft 的 NoSQL 键/属性存储 - 它具有无模式的设计，使其不同于传统的关系数据库。 采用无模式的数据存储，可以很容易地随着应用程序需求的发展使数据适应存储。 表存储易于使用，因此开发人员可以快速创建应用程序。

表存储是一种“键-属性”存储，这意味着表中的每个值都是随所键入的一个属性名称存储的。 属性名称可以用来筛选和指定选择条件。 属性集合及其值构成了实体。 因为表存储是无模式的，因此同一表中的两个实体可以包含不同的属性集合，并且这些属性可以属于不同的类型。

可以使用表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，以及服务需要的任何其他类型的元数据。 对于当前的基于 Internet 的应用程序，NoSQL 数据库（例如表存储）提供了一种用于替代传统的关系数据库的主流方式。

存储帐户可以包含任意数量的表，和一个表可以包含任意数量的实体，直至达到存储帐户容量限制。

### <a name="queue-storage"></a>队列存储
在设计应用程序以实现伸缩性时，通常要将各个应用程序组件分离，使其可以独立地进行伸缩。 队列存储为在应用程序组件之间进行异步通信提供了一种可靠的消息传送解决方案，无论这些应用程序组件是在云中、在桌面上、在本地服务器上运行还是在移动设备上运行。 队列存储还支持管理异步任务以及构建过程工作流。

存储帐户可以包含任意数目的队列，和一个队列可以包含任意数目的消息，直至达到存储帐户容量限制。 每条消息最大可以为 64 KB。

## <a name="next-steps"></a>后续步骤
* [Azure 一致存储： 差异和注意事项](azure-stack-acs-differences.md)

* 若要了解有关 Azure 存储空间的详细信息，请参阅[Microsoft Azure 存储空间简介](../../storage/common/storage-introduction.md)

