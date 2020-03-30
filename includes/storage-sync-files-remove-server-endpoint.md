---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391567"
---
否：删除服务器终结点与重新启动服务器不类似！ 删除和重新创建服务器终结点几乎绝不是修复 Azure 文件同步的同步、云分层或其他方面问题的适当解决方案。删除服务器终结点是一种破坏性操作。 如果分层文件存在于服务器终结点命名空间之外，则可能导致数据丢失。 有关详细信息[，请参阅为什么分层文件存在于服务器终结点命名空间之外](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)。 或者，它可能导致服务器终结点命名空间中存在的分层文件的无法访问文件。 重新创建服务器终结点时，这些问题无法解决。 即使从未启用过云分层，分层文件也可能存在于服务器终结点命名空间中。 因此，建议不要删除服务器终结点，除非您希望停止使用此特定文件夹的 Azure 文件同步，或者 Microsoft 工程师已明确指示这样做。 有关删除服务器终结点的详细信息，请参阅[删除服务器终结点](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)。    
