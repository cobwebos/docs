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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597767"
---
使用 Azure 存储服务加密（SSE）加密存储在 Azure 文件中的所有数据。 存储服务加密的工作方式类似于 Windows 上的 BitLocker：在文件系统级别下对数据进行加密。 由于数据是在 Azure 文件共享的文件系统下加密的，因此，在将数据编码到磁盘时，无需访问客户端上的基础密钥即可读取或写入 Azure 文件共享。

默认情况下，存储在 Azure 文件中的数据使用 Microsoft 管理的密钥加密。 使用 Microsoft 托管的密钥，Microsoft 持有加密/解密数据的密钥，并负责定期循环使用。 你还可以选择管理自己的密钥，这使你能够控制轮换过程。 如果你选择使用客户管理的密钥来加密文件共享，则 Azure 文件有权访问你的密钥，以满足来自客户端的读取和写入请求。 通过客户托管的密钥，你可以随时撤销此授权，但这意味着你的 Azure 文件共享将无法再通过 SMB 或 FileREST API 进行访问。

Azure 文件使用与其他 Azure 存储服务（如 Azure Blob 存储）相同的加密方案。 若要了解有关 Azure 存储服务加密（SSE）的详细信息，请参阅[静态数据的 Azure 存储加密](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。