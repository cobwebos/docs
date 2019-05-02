---
title: 为 Azure Marketplace 中认证 VM 映像
description: 介绍如何测试 VM 映像并提交该映像以供 Azure 市场认证。
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/26/2018
ms.author: pabutler
ms.openlocfilehash: 0dbf1abbb91f9e5c3bd2d042c57f87591d52c9cd
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938500"
---
# <a name="certify-your-vm-image"></a>认证 VM 映像

创建并部署虚拟机 (VM) 后，必须测试 VM 映像，并提交该映像以供 Azure 市场认证。 本文介绍可在何处获取“Azure 认证”的认证测试工具、如何使用此工具来认证 VM 映像，以及如何将认证结果上传到 VHD 所在的 Azure 容器。 


## <a name="download-and-run-the-certification-test-tool"></a>下载并运行认证测试工具

“Azure 认证”的认证测试工具在本地 Windows 计算机上运行，但可以测试基于 Azure 的 Windows 或 Linux VM。  它会认证用户 VM 映像是否与 Microsoft Azure 兼容，并符合有关准备 VHD 的指导和要求。 该工具的输出是一份兼容性报告，需将此报告上传到[云合作伙伴门户](https://cloudpartner.azure.com)来请求 VM 认证。

1. 下载并安装最新的[“Azure 认证”的认证测试工具](https://www.microsoft.com/download/details.aspx?id=44299)。 
2. 打开认证工具，然后单击“启动新测试”。
3. 在“测试信息”屏幕中，为测试运行输入**测试名称**。
4. 选择 VM 的**平台**：`Windows Server` 或 `Linux`。 所选的平台会影响剩余的选项。
5. 如果 VM 使用此数据库服务，请选中“针对 Azure SQL 数据库进行测试”复选框。

   ![认证测试工具初始页](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>将认证工具连接到 VM 映像

  该工具通过 [PowerShell](https://docs.microsoft.com/powershell/) 连接到基于 Windows 的 VM，通过 [SSH.Net](https://www.ssh.com/ssh/protocol/) 连接到 Linux VM。

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>将认证工具连接到 Linux VM 映像

1. 选择“SSH 身份验证”模式：`Password Authentication` 或 `key File Authentication`。
2. 如果使用基于密码的身份验证，请输入“VM DNS 名称”、“用户名”和“密码”的值。  （可选）可以更改默认的“SSH 端口”号。

     ![Linux VM 映像的密码身份验证](./media/publishvm_026.png)

3. 如果使用基于密钥文件的身份验证，请输入“VM DNS 名称”、“用户名”和“私钥”位置的值。  （可选）可以提供**通行短语**，或更改默认的“SSH 端口”号。

     ![Linux VM 映像的文件身份验证](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**将认证工具连接到基于 Windows 的 VM 映像**
1. 输入完全限定的 **VM DNS 名称**（例如 `MyVMName.Cloudapp.net`）。
2. 输入“用户名”和“密码”的值。

   ![Windows VM 映像的密码身份验证](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>运行认证测试

在认证工具中提供 VM 映像的参数值后，选择“测试连接”以确保与 VM 建立有效的连接。 验证连接后，选择“下一步”启动测试。  测试完成后，会显示包含测试结果（通过/失败/警告）的表格。  以下示例显示了 Linux VM 测试的结果。 

![Linux VM 映像的认证测试结果](./media/publishvm_029.png)

如有任何测试失败，则不会认证该映像。 在这种情况下，请查看要求和失败消息，根据指示进行更改，然后重新运行测试。 

完成自动测试后，需要在“调查表”屏幕中提供有关 VM 映像的其他信息。  此屏幕包含两个必填的选项卡。  “常规评估”选项卡包含“对/错”问答题，“内核自定义”包含多项选择题和自由形式的问题。  请回答两个选项卡中的问题，然后选择“下一步”。

![认证工具调查表](./media/publishvm_030.png)

在最后一个屏幕中可以提供额外的信息，例如 Linux VM 映像的 SSH 访问信息，并为任何未通过的评估项做出解释，以求获得例外处理。 

最后，请单击“生成报告”下载已执行的测试案例的测试结果和日志文件，以及调查表的答案。 将结果保存在 VHD 所在的同一容器中。

![保存认证测试结果](./media/publishvm_031.png)


## <a name="next-steps"></a>后续步骤

接下来，将针对提交到市场的每个 VHD [生成统一资源标识符 (URI)](./cpp-get-sas-uri.md)。 
