---
title: 无法登录到 Azure HDInsight 中的 Zeppelin
description: 无法登录到 Azure HDInsight 中的 Zeppelin
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: e9a81d458d1bab68bf94e9e9d0ebd87fac4580c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896157"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>方案：无法登录到 Azure HDInsight 中的 Apache Zeppelin

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

更改在 active directory 中添加密码后，无法登录 Apache Zeppelin。

## <a name="cause"></a>原因

`shiro_ini` 文件 `activeDirectoryRealm.systemUsername` 中提到的用户更改了 active directory 密码。

## <a name="resolution"></a>分辨率

1. 通过在 Ambari 中的 Zeppelin `shiro_ini` config 中加入 `activeDirectoryRealm.systemPassword = <new password>` 来验证更改的密码是否是根本原因。 删除 `activeDirectoryRealm.hadoopSecurityCredentialPath` 设置。 下面是 `shiro ini`的位置。

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. 如果用户现在可以在步骤1之后登录到 Zeppelin，请使用新密码创建新的 `jceks` 文件，并将 `activeDirectoryRealm.hadoopSecurityCredentialPath` 替换为新文件。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
