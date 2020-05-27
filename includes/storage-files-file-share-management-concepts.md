---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597770"
---
Azure 文件共享将部署到存储帐户。存储帐户是代表存储共享池的顶级对象。  此存储池可用于部署多个文件共享和其他存储资源，例如 Blob 容器、队列或表。 部署到存储帐户中的所有存储资源共享应用于该存储帐户的限制。 若要查看存储帐户的当前限制，请参阅 [Azure 文件存储的可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。

Azure 文件部署将使用两种主要类型的存储帐户： 
- **常规用途版本2（GPv2）存储帐户**： GPv2 存储帐户允许你将 Azure 文件共享部署在基于标准/硬盘（基于 HDD）的硬件上。 除了存储 Azure 文件共享以外，GPv2 存储帐户还可以存储其他存储资源，例如 blob 容器、队列或表。 
- **FileStorage 存储帐户**： FileStorage 存储帐户可让你在基于磁盘的基于磁盘（基于 SSD）的硬件上部署 Azure 文件共享。 FileStorage 帐户只能用于存储 Azure 文件共享;其他存储资源（blob 容器、队列、表等）都不能部署在 FileStorage 帐户中。

在 Azure 门户、PowerShell 或 CLI 中，你可能会遇到一些其他的存储帐户类型。 BlockBlobStorage 和 BlobStorage 存储帐户两个存储帐户类型不能包含 Azure 文件共享。 你可能会看到的其他两个存储帐户类型为常规用途版本1（GPv1）和经典存储帐户，两者都可以包含 Azure 文件共享。 尽管 GPv1 和经典存储帐户可能包含 Azure 文件共享，但 Azure 文件的大多数新功能只能在 GPv2 和 FileStorage 存储帐户中使用。 因此，我们建议仅将 GPv2 和 FileStorage 存储帐户用于新部署，并在你的环境中升级 GPv1 和经典存储帐户。  