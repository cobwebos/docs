---
title: 无法登录到 Azure HDInsight 中的 Zeppelin
description: 无法登录到 Azure HDInsight 中的 Zeppelin
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977399"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>方案:无法登录到 Azure HDInsight 中的 Apache Zeppelin

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

更改在 active directory 中添加密码后, 无法登录 Apache Zeppelin。

## <a name="cause"></a>原因

`shiro_ini`文件的中`activeDirectoryRealm.systemUsername`提到的用户更改了 active directory 密码。

## <a name="resolution"></a>解决

1. 通过在 Ambari 中包括`activeDirectoryRealm.systemPassword = <new password>` Zeppelin `shiro_ini` config, 验证更改的密码是否是根本原因。 `activeDirectoryRealm.hadoopSecurityCredentialPath`删除设置。 下面是的`shiro ini`位置。

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. 如果用户现在可以在步骤1之后登录到 Zeppelin, 请使用新`jceks`密码创建一个新文件, 并将`activeDirectoryRealm.hadoopSecurityCredentialPath`替换为新文件。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
