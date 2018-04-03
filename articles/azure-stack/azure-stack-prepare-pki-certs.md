---
title: 为 Azure Stack 集成系统部署准备 Azure Stack 公钥基础结构证书 | Microsoft Docs
description: 介绍如何为 Azure Stack 集成系统准备 Azure Stack PKI 证书。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: c195cc0bacd9eea7e75fa35cd155845f03dd21cf
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>为部署准备 Azure Stack PKI 证书
必须使用与 Azure Stack的证书要求匹配的属性导入和导出[从所选 CA 获取](azure-stack-get-pki-certs.md)的证书文件。


## <a name="prepare-certificates-for-deployment"></a>为部署准备证书
使用以下步骤来准备和验证 Azure Stack PKI 证书： 

1.  将[从所选 CA 获取](azure-stack-get-pki-certs.md)的原始证书版本复制到部署主机上的目录。 
  > [!WARNING]
  > 如果已以任何方式导入、导出或更改直接由 CA 提供的文件，请勿复制该文件。

2.  将证书导入到本地计算机证书存储：

    a.  右键单击证书并选择“安装 PFX”。

    b.  在**证书导入向导**中，选择“本地计算机”作为导入位置。 选择“**下一步**”。

    ![本地计算机导入位置](.\media\prepare-pki-certs\1.png)

    c.  在“选择要导入的文件”页上选择“下一步”。

    d.单击“下一步”。  在“私钥保护”页上，输入证书文件的密码，然后启用“将此密钥标记为可导出。这可让你在稍后备份或传输密钥”选项。 选择“**下一步**”。

    ![将密钥标记为可导出](.\media\prepare-pki-certs\2.png)

    e.  选择“将所有证书放在以下存储中”，然后选择“企业信任”作为位置。 单击“确定”以关闭“证书存储选择”对话框，然后单击“下一步”。

    ![配置证书存储](.\media\prepare-pki-certs\3.png)

  f.    单击“完成”以完成证书导入向导。

  g.    针对为部署提供的所有证书重复此过程。

3. 按照 Azure Stack 的需求将证书导出为 PFX 文件格式：

  a.    打开证书管理员 MMC 控制台并连接到本地计算机证书存储。

  b.    转到“企业信任”目录。

  c.    选择在前面的步骤 2 中导入的其中一个证书。

  d.单击“下一步”。    从证书管理员控制台的任务栏中，选择“操作” > “所有任务” > “导出”。

  e.    选择“**下一步**”。

  f.    选择“是，导出私钥”，并单击“下一步”。

  g.    在“导出文件格式”部分中，选择“导出所有扩展属性”，然后单击“下一步”。

  h.    选择“密码”并为证书提供密码。 请记住此密码，因为它将用作部署参数。 选择“**下一步**”。

  i.    选择要导出的 pfx 文件的文件名和位置。 选择“**下一步**”。

  j.    选择“完成”。

  k.    针对在前面的步骤 2 中为部署导入的所有证书重复此过程。

## <a name="next-steps"></a>后续步骤
[验证 PKI 证书](azure-stack-validate-pki-certs.md)