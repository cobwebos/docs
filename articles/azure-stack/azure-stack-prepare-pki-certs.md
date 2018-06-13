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
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203470"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>为部署准备 Azure Stack PKI 证书
必须使用与 Azure Stack的证书要求匹配的属性导入和导出[从所选 CA 获取](azure-stack-get-pki-certs.md)的证书文件。


## <a name="prepare-certificates-for-deployment"></a>为部署准备证书
使用以下步骤来准备和验证 Azure Stack PKI 证书： 

### <a name="import-the-certificate"></a>导入证书

1.  将[从所选 CA 获取](azure-stack-get-pki-certs.md)的原始证书版本复制到部署主机上的目录。 
  > [!WARNING]
  > 如果已以任何方式导入、导出或更改直接由 CA 提供的文件，请勿复制该文件。

2.  右键单击证书并选择**安装证书**或**安装 PFX**具体取决于从你的 CA 证书已传递方式。

3. 在**证书导入向导**中，选择“本地计算机”作为导入位置。 选择“**下一步**”。 在以下屏幕上，单击下一步试。

    ![本地计算机导入位置](.\media\prepare-pki-certs\1.png)

4.  选择“将所有证书放在以下存储中”，然后选择“企业信任”作为位置。 单击“确定”以关闭“证书存储选择”对话框，然后单击“下一步”。

    ![配置证书存储](.\media\prepare-pki-certs\3.png)

    a. 如果您要导入 PFX 你将看到与其他对话框。 在“私钥保护”页上，输入证书文件的密码，然后启用“将此密钥标记为可导出。这可让你在稍后备份或传输密钥”选项。 选择“**下一步**”。

    ![将密钥标记为可导出](.\media\prepare-pki-certs\2.png)

5. 单击完成以完成导入。

### <a name="export-the-certificate"></a>导出证书

打开证书管理员 MMC 控制台并连接到本地计算机证书存储。

1. 打开 Microsoft 管理控制台，在 Windows 10 中右键单击开始菜单，然后单击运行。 类型**mmc**单击确定。

2. 单击文件添加/删除管理单元中，然后选择证书，单击添加。

    ![添加证书管理单元](.\media\prepare-pki-certs\mmc-2.png)
 
3. 选择计算机帐户，单击下一步，然后选择本地计算机然后完成。 单击确定以关闭添加/删除管理单元页面。

    ![添加证书管理单元](.\media\prepare-pki-certs\mmc-3.png)

4. 浏览到证书 > 企业信任 > 证书位置。 验证在右侧看到你的证书。

5. 从证书管理员控制台的任务栏中，选择“操作” > “所有任务” > “导出”。 选择“**下一步**”。

  > [!NOTE]
  > 具体取决于多少 Azure 堆栈证书具有你可能需要一次完成此过程。

4. 选择“是，导出私钥”，并单击“下一步”。

5. 在“导出文件格式”部分中，选择“导出所有扩展属性”，然后单击“下一步”。

6. 选择“密码”并为证书提供密码。 请记住此密码，因为它将用作部署参数。 选择“**下一步**”。

7. 选择要导出的 pfx 文件的文件名和位置。 选择“**下一步**”。

8. 选择“完成”。

## <a name="next-steps"></a>后续步骤
[验证 PKI 证书](azure-stack-validate-pki-certs.md)