---
title: 关于 Azure 托管 HSM 安全域
description: 托管 HSM 安全域概述，恢复托管 HSM 所需的一组核心凭据
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994643"
---
# <a name="about-the-managed-hsm-security-domain"></a>关于托管 HSM 安全域

托管 HSM 安全域 (SD) 是在发生灾难时恢复托管 HSM 所需的一组核心凭据。 安全域在托管 HSM 硬件和服务软件 enclaves 中生成，表示 HSM 的 "所有权"。

每个托管 HSM 都必须有一个要运行的安全域。 当你请求新的托管 HSM 时，它将被预配，但在你下载安全域之前不会被激活。 如果托管 HSM 处于预配状态，但未激活，则可以通过两种方式来激活它：
- 下载安全域是默认方法，并允许你安全地存储安全域，以与其他托管 HSM 一起使用或从总体灾难中恢复。
- 上传现有的安全域，这允许你创建多个共享同一安全域的托管 HSM 实例。

## <a name="download-your-security-domain"></a>下载安全域

如果预配了托管 HSM 但未激活，则下载安全域将捕获从所有硬件完全丢失中恢复所需的核心凭据。 若要下载 Security 域，必须创建至少3个 (最多10个) RSA 密钥对，并在请求安全域下载时将这些公钥发送到该服务。 还需要指定 (仲裁) 在将来对安全域进行解密所需的最小密钥数量。 托管 HSM 将初始化安全域，并使用 [rivest-shamir-adleman 的机密共享算法](https://dl.acm.org/doi/10.1145/359168.359176)提供的公钥对其进行加密。 仅当至少有可用的私钥仲裁时，才可以对下载的安全域 blob 进行解密;必须确保私钥安全，以确保安全域的安全性。 下载完成后，托管 HSM 将处于 "已激活" 状态以供使用。  

> [!IMPORTANT]
> 对于完整的灾难恢复，你必须拥有安全域和用于保护它的私钥的仲裁以及完整的 HSM 备份。 如果你丢失了安全域或足够的 RSA 密钥 (私钥) 丢失仲裁，并且不存在托管 HSM 正在运行的实例，则不可能进行灾难恢复。

## <a name="upload-a-security-domain"></a>上传安全域

如果预配了托管 HSM 但未激活，则可以启动安全域恢复过程。 托管 HSM 将生成 RSA 密钥对，并返回公钥。 然后，可以将安全域上传到托管 HSM。 在上传之前，需要为客户端 (Azure CLI 或 PowerShell) 提供下载安全域时所用的最小仲裁数目的私钥。 客户端将使用此仲裁来解密安全域，并使用在你请求恢复时下载的公钥对其进行重新加密。 上传完成后，托管 HSM 将处于激活状态。

## <a name="backup-and-restore-behavior"></a>备份和还原行为

只有在) 创建备份的源托管 HSM (，以及要在其中还原备份的目标托管 HSM () 共享同一安全域时，才能成功还原 (完整备份或单个密钥备份) 的备份。 通过这种方式，安全域还定义了每个托管 HSM 的加密边界。

## <a name="next-steps"></a>后续步骤

- 请参阅[托管 HSM 概述](overview.md)
- 了解如何 [通过 Azure CLI 管理托管 HSM](key-management.md)
- 查看[托管 HSM 最佳做法](best-practices.md)
