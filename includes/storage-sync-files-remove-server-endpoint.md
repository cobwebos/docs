---
title: include 文件
description: include 文件
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738677"
---
注意：删除服务器终结点不同于重启服务器！ 在绝大多数情况下，删除或重新创建服务器终结点不是适合修复同步、云分层或 Azure 文件同步其他方面问题的解决方案。删除服务器终结点是一种破坏性操作。对于存在于服务器终结点命名空间之外的分层文件（有关更多信息，请参阅[为什么分层文件存在于服务器终结点命名空间之外](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)），或者对于位于服务器终结点命名空间之内的分层文件的不可访问文件，该操作可能导致数据丢失。 重新创建服务器终结点后，这些问题并不会得到解决。 即使从未启用过云分层，分层文件也可能存在于服务器终结点命名空间中。 因此，不建议删除服务器终结点，除非想对特定文件夹停用 Azure 文件同步，或者 Microsoft 工程师明确指示你采取该操作。 有关删除服务器终结点的详细信息，请参阅[删除服务器终结点](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)。    