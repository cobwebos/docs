---
title: 将文件夹结构映射到 Azure 文件同步拓扑
description: 将现有文件和文件夹结构映射到 Azure 文件共享，以便与 Azure 文件同步一起使用。在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124726"
---
在此步骤中，您将评估所需的 Azure 文件共享数。 单个 Windows 服务器（或群集）最多可以同步 30 个 Azure 文件共享。

卷上可能有更多的文件夹，您当前以 SMB 共享方式在本地共享给用户和应用。 最简单的方法是设想本地共享将 1：1 映射到 Azure 文件共享。 如果单个 Windows 服务器的数字足够小，低于 30，则建议使用 1：1 映射。

如果共享数超过 30，则通常不需要将本地共享 1：1 映射到 Azure 文件共享。
请考虑以下选项：

#### <a name="share-grouping"></a>共享分组

例如，如果人力资源部门共有 15 个共享，则可以考虑将所有 HR 数据存储在单个 Azure 文件共享中。 在一个 Azure 文件共享中存储多个本地共享不会阻止您在本地 Windows 服务器上创建通常的 15 个 SMB 共享。 这仅意味着您将这 15 个共享的根文件夹组织为公共文件夹下的子文件夹。 然后，将此公用文件夹同步到 Azure 文件共享。 这样，此一组本地共享只需要云中的单个 Azure 文件共享。

#### <a name="volume-sync"></a>卷同步

Azure 文件同步支持将卷的根部同步到 Azure 文件共享。
如果同步根文件夹，则所有子文件夹和文件将最终位于同一 Azure 文件共享中。

合成卷的根并不总是最好的答案。 同步多个位置的好处是，这样做有助于降低每个同步作用域的项目数。 使用少量项目设置 Azure 文件同步不仅有利于文件同步。项目数量减少还有利于其他方案，如：

* 从作为备份拍摄的 Azure 文件共享快照的云端还原
* 本地服务器的灾难恢复可以显著加快速度
* 可以直接在 Azure 文件共享（同步之外）进行的更改可以更快地检测和同步

#### <a name="a-structured-approach-to-a-deployment-map"></a>部署映射的结构化方法

在后续步骤中部署云存储之前，在本地文件夹和 Azure 文件共享之间创建映射非常重要。 然后，此映射将通知将预配的 Azure 文件同步"同步组"资源数和哪些资源。 同步组将 Azure 文件共享和服务器上的文件夹连接在一起，并建立同步连接。

要决定需要多少 Azure 文件共享，请查看以下限制和最佳实践。 这样做将帮助您优化地图：

* 安装了 Azure 文件同步代理的服务器最多可与 30 个 Azure 文件共享同步。
* Azure 文件共享部署在存储帐户中。 这使得存储帐户成为性能数字（如 IOPS 和吞吐量）的规模目标。 从理论上讲，两个标准（非高级）Azure 文件共享可以饱和存储帐户所能提供的最大性能。 如果计划仅将 Azure 文件同步附加到这些文件共享，则将多个 Azure 文件共享分组到同一存储帐户不会造成问题。 查看 Azure 文件共享性能目标，深入了解要考虑的相关指标。 如果计划将应用提升到 Azure，该应用将本机使用 Azure 文件共享，则可能需要 Azure 文件共享的更多性能。 如果这是一种可能性，即使将来也是如此，那么将 Azure 文件共享映射到其自己的存储帐户是最佳的。
* 单个 Azure 区域中每个订阅有 250 个存储帐户的限制。

> [!TIP]
> 考虑到此信息，通常需要将卷上的多个顶级文件夹分组到一个常见的新根目录中。 然后，您将此新根目录和分组到该目录中的所有文件夹同步到单个 Azure 文件共享。                                                    

此技术允许您保持在每个服务器的 30 个 Azure 文件共享同步限制内。
公共根项下的此分组不会影响对数据的访问。 您的 ACL 保持不变，您只需调整服务器文件夹中可能具有的任何共享路径（如 SMB 或 NFS 共享），现在更改为公共根目录。 没有其他改变。

Azure 文件同步的另一个重要方面以及平衡的性能和体验是了解 Azure 文件同步性能的规模因素。 显然，当文件在互联网上同步时，较大的文件需要更多的时间和带宽来同步。

> [!IMPORTANT]
> Azure 文件同步最重要的缩放矢量是需要同步的项目（文件和文件夹）数。

Azure 文件同步支持将多达 100，000 个项目同步到单个 Azure 文件共享。 可以超出此限制，并且仅描述 Azure 文件同步团队定期测试的内容。

最佳做法是保持每个同步作用域的项数较低。 在将文件夹映射到 Azure 文件共享时，需要考虑这一方面。

即使在您的情况下，一组文件夹可以在逻辑上同步到同一个 Azure 文件共享（使用上面新的通用根文件夹方法），最好还是重新组合文件夹，以便它们同步到两个而不是一个 Azure 文件共享。 此方法可用于保持服务器中每个文件共享的文件和文件夹数的平衡。

#### <a name="create-a-mapping-table"></a>创建映射表

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        使用前面概念的组合来帮助确定需要多少 Azure 文件共享，以及现有数据的哪些部分最终将位于 Azure 文件共享的哪个部分。
        
        创建一个表来记录您的想法，以便您可以在下一步中引用它。 保持井然有序很重要，因为在同时预配许多 Azure 资源时，很容易丢失映射计划的详细信息。 为了帮助您创建完整的映射，您可以将 Microsoft Excel 文件下载为模板。

[//]: # (HTML 似乎是在同一行上添加具有工作图像解析和文本/超链接的嵌套两列表的唯一方法。)

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
