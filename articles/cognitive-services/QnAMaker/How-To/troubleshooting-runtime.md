---
title: 故障排除 - QnAMaker
titlesuffix: Azure Cognitive Services
description: QnAMaker 由托管在用户的 Azure 帐户的组件组成。 若要进行调试工作，用户可能需要对其 QnAMaker Azure 资源进行操作，或向 QnAMaker 支持团队提供与设置相关的其他信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5b65a121e895b4855c7c69d2b67e7055c88ddd08
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466049"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>用于支持 QnA Maker 服务和运行时的故障排除技巧
QnAMaker 由托管在用户的 Azure 帐户的组件组成。 若要进行调试工作，用户可能需要对其 QnAMaker Azure 资源进行操作，或向 QnAMaker 支持团队提供与设置相关的其他信息。

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>如何获取最新的 QnAMaker 运行时更新
QnAMaker 运行时是在 Azure 门户中[创建 QnAMaker 服务](./set-up-qnamaker-service-azure.md)时所部署的 Azure 应用服务的一部分。 对运行时的更新定期进行。 QnA Maker 应用服务是在自动更新模式 post 我们 Apr 2019 站点扩展版本 （版本 5 +）。 这已用于在升级期间处理的零停机时间。 您可以检查在当前版本 https://www.qnamaker.ai/UserSettings 。 如果你的版本早于版本 5.x，您必须重新启动应用服务以应用最新的更新，以将应用于 QnAMaker 安装程序。

1. 在 [Azure 门户](https://portal.azure.com)中转到 QnAMaker 服务（资源组）

    ![QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. 单击“应用服务”并打开“概述”部分

     ![QnAMaker 应用服务](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. 重启应用服务。 应该会在几秒钟内完成。 请注意，重启期间将不能使用此 QnAMaker 服务上生成的下游应用程序/聊天机器人。

    ![QnAMaker 应用服务重启](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>如何获取 QnAMaker 服务主机名
在联系 QnAMaker 客户支持部门或 UserVoice 时，QnAMaker 服务主机名对调试工作非常有用。 主机名采用以下形式的 URL： https://{hostname}.azurewebsites.net  。
    
1. 在 [Azure 门户](https://portal.azure.com)中转到 QnAMaker 服务（资源组）

    ![Azure 门户中的 QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. 单击“应用服务”

     ![选择“QnAMaker 应用服务”](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. 在“概述”部分可以找到主机名 URL

    ![QnAMaker 主机名](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用主动学习改进知识库问题](./improve-knowledge-base.md)
