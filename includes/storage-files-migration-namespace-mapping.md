---
title: 将文件夹结构映射到 Azure 文件同步拓扑
description: 将现有文件和文件夹结构映射到 Azure 文件共享以便与 Azure 文件同步一起使用。在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209553"
---
在此步骤中，你将评估你需要多少个 Azure 文件共享。 单个 Windows 服务器（或群集）最多可以同步30个 Azure 文件共享。

你的 StorSimple 上可能有更多文件夹，当前作为 SMB 共享本地共享到你的用户和应用。 最简单的方法是构想本地共享将1:1 映射到 Azure 文件共享。 如果此数字 manageably 小，表示在单个 Windows Server 上小于30，或者计划有两个 Windows Server （60）等，则建议使用1:1 映射。

如果共享超过30个，通常不需要将本地共享1:1 映射到 Azure 文件共享。
请考虑以下选项：

#### <a name="share-grouping"></a>共享分组

例如，如果你的 HR 部门总共有15个共享，则可以考虑将所有 HR 数据存储在一个 Azure 文件共享中。 将多个本地共享存储在一个 Azure 文件共享中不会阻止你在本地 Windows Server 上创建通常的15个 SMB 共享。 这只意味着将这些15个共享的根文件夹组织为公用文件夹下的子文件夹。 然后，将此公用文件夹同步到 Azure 文件共享。 这样一来，就只需要将云中的单个 Azure 文件共享用于此组本地共享。

#### <a name="volume-sync"></a>卷同步

Azure 文件同步支持将卷的根同步到 Azure 文件共享。
如果同步根文件夹，则所有子文件夹和文件将在相同的 Azure 文件共享中结束。

#### <a name="other-best-practices-to-consider"></a>其他要考虑的最佳方案

除每台服务器30个 Azure 文件共享同步限制之外，最主要的考虑因素是同步的效率。

如果服务器上有多个共享与各自的 Azure 文件共享同步，则同步可以并行工作。 缩放向量不是同步范围内所有文件的大小。 这是需要处理的项目（文件和文件夹）的数量。

单个 Azure 文件共享最多可容纳 100 TiB。
Azure 文件同步支持对每个 Azure 文件共享最多支持100000000项同步。

同步根卷并不一定是最好的办法。 同步多个位置有一些优点，这样做有助于使每个同步作用域的项数保持较低。 使用较少数量的项设置 Azure 文件同步不仅对同步非常重要，而且还可以从备份中获益，还可以协助灾难恢复的速度，以防你丢失服务器并预配连接到同一 Azure fil 的新服务e 共享。

结合使用上述概念来帮助确定所需的 Azure 文件共享的数量，以及现有 StorSimple 数据的哪些部分将在哪个 Azure 文件共享中结束。

创建记录你的想法的列表，以便你可以在下一步中引用它。 此处的组织功能非常重要，因为一次预配多个资源时，可以轻松丢失映射计划的详细信息。
