---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597767"
---
存储在 Azure 文件中的所有数据都使用 Azure 存储服务加密 （SSE） 在静态加密。 存储服务加密的工作方式与 Windows 上的 BitLocker 类似：数据在文件系统级别下加密。 由于数据在 Azure 文件共享的文件系统下进行加密，因为它已编码到磁盘，因此不必访问客户端上的基础密钥来读取或写入 Azure 文件共享。

默认情况下，存储在 Azure 文件中的数据使用 Microsoft 管理的密钥进行加密。 使用 Microsoft 管理的密钥，Microsoft 拥有加密/解密数据的密钥，并负责定期轮换数据。 您还可以选择管理自己的密钥，从而控制旋转过程。 如果选择使用客户管理的密钥加密文件共享，Azure 文件将有权访问密钥以完成来自客户端的读取和写入请求。 使用客户管理的密钥，您可以随时撤消此授权，但这意味着 Azure 文件共享将不再通过 SMB 或 FileREST API 访问。

Azure 文件使用与其他 Azure 存储服务（如 Azure Blob 存储）相同的加密方案。 要了解有关 Azure 存储服务加密 （SSE） 的更多，请参阅[Azure 存储加密以了解静态数据](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。