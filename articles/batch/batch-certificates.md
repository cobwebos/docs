---
title: 使用证书 - Azure Batch
description: 使用证书来启用应用程序身份验证
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146379"
---
# <a name="using-certificates-with-batch"></a>将证书与 Batch 配合使用

目前，将证书与 Batch 一起使用的主要原因是，在需要通过终结点进行身份验证的池中有运行的应用程序。 

如果还没有证书，可以使用 `makecert` 命令行工具创建自签名证书。

## <a name="upload-certificates-manually-through-the-azure-portal"></a>通过 Azure 门户手动上传证书

1. 从要将证书上传到的 Batch 帐户中，选择“证书”，然后选择“添加”。 

2. 上传有 .pfx 或 .cer 扩展名的证书。 

上传后，相应的证书将添加到证书列表，你可以验证指纹。

现在，在创建 Batch 池时，可以导航到池中的证书，并将上传的证书分配给该池。

## <a name="next-steps"></a>后续步骤

Batch 具有证书 API [AZ batch 证书创建](/cli/azure/batch/certificate)

有关使用 Key Vault 的信息，请参阅[使用 Batch 安全地访问 Key Vault](credential-access-key-vault.md)。
