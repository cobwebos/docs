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
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563418"
---
使用 Azure 存储服务加密 (SSE) 对存储在 Azure 文件存储中的所有数据进行静态加密。 存储服务加密的工作方式类似于 Windows 上的 BitLocker：在文件系统级别下对数据进行加密。 由于数据在 Azure 文件共享的文件系统下加密，因此，在将数据编码到磁盘时，无需访问客户端上的基础密钥即可读取或写入 Azure 文件共享。 静态加密同时适用于 SMB 和 NFS 协议。

默认情况下，Azure 文件存储中存储的数据使用 Microsoft 管理的密钥进行加密。 通过 Microsoft 管理的密钥，Microsoft 持有加密/解密数据的密钥，且负责定期轮换这些密钥。 你还选择管理你自己的密钥，这让你能够控制密钥轮换过程。 如果你选择使用客户管理的密钥来加密你的文件共享，则 Azure 文件存储可访问你的密钥来执行来自你的客户端的读取和写入请求。 通过客户管理的密钥，你可随时撤销此授权，但撤销意味着将无法再通过 SMB 或 FileREST API 访问你的 Azure 文件共享。

Azure 文件存储预其他 Azure 存储服务（例如 Azure Blob 存储）使用相同的机密方案。 要详细了解 Azure 存储服务加密 (SSE)，请参阅[针对静态数据的 Azure 存储加密](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。