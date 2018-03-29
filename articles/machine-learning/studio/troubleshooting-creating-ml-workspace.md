---
title: 疑难解答：创建并连接到机器学习工作区 | Microsoft Docs
description: 创建并连接到 Azure 机器学习工作区中常见问题的解决方案
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5c265b14a88e993512811de365f1ba51f7ba6028
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>疑难解答指南：创建并连接到机器学习工作区
本指南介绍了设置 Azure 机器学习工作区时一些常见难题的解决方案。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>工作区所有者
若要在机器学习工作室中打开工作区，则必须登录到用于创建工作区的 Microsoft 帐户，或者需要收到所有者发出的邀请以加入工作区。 在 Azure 门户中可以管理工作区，包括配置访问权限。

有关管理工作区的详细信息，请参阅[管理 Azure 机器学习工作区]。

[管理 Azure 机器学习工作区]: manage-workspace.md

## <a name="allowed-regions"></a>允许的区域
机器学习当前只允许有限数量的区域。 如果订阅不包括这些区域之一，可能会看到错误消息，“你在允许的区域中没有订阅。”

要请求将某区域添加到订阅，可从 Azure 门户创建新的 Microsoft 支持请求，将“计费”选为问题类型，然后按照提示提交请求。

## <a name="storage-account"></a>存储帐户
机器学习服务需要存储帐户以存储数据。 可以使用现有存储帐户，或者可以在创建新的机器学习工作区时创建新的存储帐户（如果有创建新的存储帐户的配额）。

创建新的机器学习工作区之后，可以使用用于创建工作区的 Microsoft 帐户登录到机器学习工作室。 如果遇到错误消息，“未找到工作区”（类似于下面的屏幕快照），请使用以下步骤删除浏览器 cookie。

![未找到工作区][screen3]

**删除浏览器 cookie**

1. 如果使用 Internet Explorer，请单击右上角的“工具”按钮，并选择“Internet 选项”。  

![Internet 选项][screen4]

2. 在“常规”选项卡下，单击“删除...”

![“常规”选项卡][screen5]

3. 在“删除浏览历史记录”对话框中，确保已选中“Cookie 和网站数据”，并单击“删除”。

![删除 Cookie][screen6]

删除 Cookie 之后，重启浏览器，并转到 [Microsoft Azure 机器学习页](https://studio.azureml.net)。 如果系统提示输入用户名和密码，请输入用于创建工作区的同一个 Microsoft 帐户。

## <a name="comments"></a>注释

目标是使机器学习体验尽可能流畅。 请在 [Azure 机器学习论坛](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)上任意发表评论和问题，帮助我们改进服务。

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
