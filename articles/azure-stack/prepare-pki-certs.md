---
title: "为集成的 Azure 堆栈系统部署准备 Azure 堆栈公钥基础结构证书 |Microsoft 文档"
description: "描述如何为 Azure 堆栈集成系统准备 Azure 堆栈 PKI 证书。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: b2f1e9bf62773dd7124678552f23dd9262530031
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>为部署准备 Azure 堆栈 PKI 证书
证书文件[从选择的 CA 获取](azure-stack-get-pki-certs.md)必须导入和导出其属性与匹配 Azure 堆栈的证书要求。


## <a name="prepare-certificates-for-deployment"></a>为部署准备证书
使用以下步骤来准备和验证 Azure 堆栈 PKI 证书： 

1.  将复制的原始证书版本[从选择的 CA 获取](azure-stack-get-pki-certs.md)到部署主机上的目录。 
  > [!WARNING]
  > 以任何方式从直接由 CA 提供的文件不复制具有已导入、 导出，或更改的文件。

2.  导入到本地计算机证书存储的证书：

    a.  右键单击证书并选择**安装 PFX**。

    b.  在**证书导入向导**，选择**本地计算机**作为导入位置。 选择“**下一步**”。

    ![本地计算机导入位置](.\media\prepare-pki-certs\1.png)

    c.  选择**下一步**上**选择要导入的文件**页。

    d.单击“下一步”。  上**私钥保护**页上，输入您的证书文件的密码，然后启用**标志此密钥为可导出。此选项，可以备份或传输你的密钥在更高版本时**选项。 选择“**下一步**”。

    ![标记密钥为可导出](.\media\prepare-pki-certs\2.png)

    e.  选择**所有入下列存储证书的位置**，然后选择**企业信任**作为位置。 单击**确定**以关闭证书存储区选择对话框中，然后**下一步**。

    ![配置证书存储区](.\media\prepare-pki-certs\3.png)

  f.    单击**完成**完成证书导入向导。

  g.    重复此过程为你要为你的部署提供的所有证书。

3. 将证书导出为 PFX 文件格式与 Azure 堆栈的要求：

  a.    打开证书管理器 MMC 控制台并连接到本地计算机证书存储。

  b.    转到**企业信任**目录。

  c.    在上面的步骤 2 中选择你导入的证书之一。

  d.单击“下一步”。    从任务栏的证书管理器控制台中，选择**操作** > **所有任务** > **导出**。

  e.    选择“**下一步**”。

  f.    选择**是，导出私钥**，然后单击**下一步**。

  g.    在导出文件格式部分中，选择**导出所有扩展属性**，然后单击**下一步**。

  h.    选择**密码**和为证书提供密码。 请记住此密码，因为它用作部署参数。 选择“**下一步**”。

  i.    选择的文件名称和要导出的 pfx 文件的位置。 选择“**下一步**”。

  j.    选择“完成”。

  k.    你为你的部署在上面的步骤 2 中导入的证书的所有重复此过程。

## <a name="next-steps"></a>后续步骤
[验证 PKI 证书](validate-pki-certs.md)