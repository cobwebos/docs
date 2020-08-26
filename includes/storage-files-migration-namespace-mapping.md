---
title: 将文件夹结构映射到 Azure 文件同步拓扑
description: 将现有文件和文件夹结构映射到 Azure 文件共享以便与 Azure 文件同步一起使用。跨迁移文档共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424976"
---
在此步骤中，你将评估你需要多少个 Azure 文件共享。 单个 Windows Server 实例（或群集）最多可以同步30个 Azure 文件共享。

你的卷上可能有更多文件夹在本地作为 SMB 共享本地共享到用户和应用。 最简单的方法是构想将1:1 映射到 Azure 文件共享的本地共享。 如果你有一个小得多的数字，请在低于30的单个 Windows Server 实例之前，建议使用1:1 映射。

如果共享超过30个，通常不需要将本地共享1:1 映射到 Azure 文件共享。 请考虑以下选项。

#### <a name="share-grouping"></a>共享分组

如果你的 HR 部门（例如）总共有15个共享，则可以考虑将所有 HR 数据存储在一个 Azure 文件共享中。 如果将多个本地共享存储在一个 Azure 文件共享中，则不会阻止你在本地 Windows Server 实例上创建常见的15个 SMB 共享。 这只意味着将这些15个共享的根文件夹组织为公用文件夹下的子文件夹。 然后，将此公用文件夹同步到 Azure 文件共享。 这样一来，就只需要将云中的单个 Azure 文件共享用于此组本地共享。

#### <a name="volume-sync"></a>卷同步

Azure 文件同步支持将卷的根同步到 Azure 文件共享。 如果同步根文件夹，则所有子文件夹和文件都将转为相同的 Azure 文件共享。

同步卷的根目录并非总是最好的答案。 同步多个位置有一些好处。 例如，这样做有助于使每个同步作用域的项数保持较低。 使用较少数量的项目设置 Azure 文件同步不仅对文件同步有利。项目数量越少，还会受益于以下方案：

* 可以将 Azure 文件共享快照中的云端还原作为备份来执行。
* 本地服务器的灾难恢复可以显著提高速度。
* 可以更快地检测和同步 Azure 文件共享中直接进行的更改。

#### <a name="a-structured-approach-to-a-deployment-map"></a>部署映射的结构化方法

在稍后的步骤中部署云存储之前，请务必在本地文件夹和 Azure 文件共享之间创建映射。 然后，此映射会通知要预配多少个 Azure 文件同步*同步组*资源。 同步组会将服务器上的 Azure 文件共享和文件夹连接在一起，并建立同步连接。

若要确定需要多少个 Azure 文件共享，请查看以下限制和最佳实践。 这样做有助于优化地图。

* 安装了 Azure 文件同步代理的服务器可与最多30个 Azure 文件共享同步。
* Azure 文件共享部署在存储帐户中。 这会使存储帐户成为性能数字（如 IOPS 和吞吐量）的缩放目标。 

  理论上，两种标准（而非高级） Azure 文件共享都能分担存储帐户可提供的最大性能。 如果你计划仅将 Azure 文件同步附加到这些文件共享，则将多个 Azure 文件共享组合到相同的存储帐户不会产生问题。 查看 Azure 文件共享性能目标，深入了解要考虑的相关指标。 

  如果你计划将应用程序提升到 Azure，以便在本地使用 Azure 文件共享，则你可能需要从 Azure 文件共享获得更高的性能。 如果这是一个可能的情况，甚至在将来，都最好将 Azure 文件共享映射到其自己的存储帐户。
* 单个 Azure 区域中，每个订阅的存储帐户数限制为250。

> [!TIP]
> 考虑到此信息，通常需要将卷上的多个顶级文件夹分组到一个公用的新根目录中。 然后，将这个新的根目录以及您分组到其中的所有文件夹同步到单个 Azure 文件共享。 此方法允许你在每台服务器30个 Azure 文件共享同步的限制范围内保持不变。
>
> 在公用根下进行此分组不会对数据访问产生任何影响。 你的 Acl 保持原样。 你只需调整你现在已更改为公用根的服务器文件夹上的任何共享路径（如 SMB 或 NFS 共享）。 无其他更改。

Azure 文件同步的另一个重要方面以及平衡的性能和经验都是了解 Azure 文件同步性能的规模因素。 显然，当文件通过 internet 同步时，较大的文件需要更多的时间和带宽来进行同步。

> [!IMPORTANT]
> Azure 文件同步的最重要缩放矢量是需要同步的项目（文件和文件夹）的数量。

Azure 文件同步最多支持将100000000项同步到单个 Azure 文件共享。 此限制可能会超出，并仅显示 Azure 文件同步团队定期测试的内容。

最佳做法是保持每个同步范围内的项的数目较低。 这是在将文件夹映射到 Azure 文件共享时要考虑的重要因素。

在您的情况下，可能会有一组文件夹以逻辑方式同步到相同的 Azure 文件共享（使用前面提到的新的通用根文件夹方法）。 但最好是重新组合文件夹，使它们同步到两个（而不是一个） Azure 文件共享。 您可以使用此方法来使每个文件共享的文件和文件夹数在服务器之间保持平衡。

#### <a name="create-a-mapping-table"></a>创建映射表

:::row:::
    :::column:::
        [![映射表的示例。下载以下文件以体验并使用此映像的内容。](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        结合使用前面的概念来帮助确定所需的 Azure 文件共享的数量，以及现有数据的哪些部分最终会在哪个 Azure 文件共享位置结束。
        
        创建记录你的想法的表，以便可以在下一步中引用它。 保持井然有序非常重要，因为当你同时预配许多 Azure 资源时，可能会很容易丢失映射计划的详细信息。 为了帮助你创建完整的映射，你可以下载 Microsoft Excel 文件作为模板。

[//]: # (HTML 显示为在同一行中使用工作图像分析和文本/超链接添加嵌套的双列表表的唯一方法。)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">下载命名空间映射模板。</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
