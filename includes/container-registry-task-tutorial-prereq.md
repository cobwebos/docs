---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 6e0175173f17ae0958522517360b94ee80f3b2f9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148974"
---
## <a name="prerequisites"></a>先决条件

### <a name="get-sample-code"></a>获取示例代码

本教程假设你已完成[前面教程](../articles/container-registry/container-registry-tutorial-quick-task.md)中的步骤，并且已经创建分支和克隆示例存储库。 如果尚未完成，请先完成前面教程[先决条件](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites)部分中的步骤，再继续操作。

### <a name="container-registry"></a>容器注册表

Azure 订阅中必须具有 Azure 容器注册表才能完成此教程。 如果需要注册表，请参阅[上一教程](../articles/container-registry/container-registry-tutorial-quick-task.md)或[快速入门：使用 Azure CLI 创建容器注册表](../articles/container-registry/container-registry-get-started-azure-cli.md)。

## <a name="create-a-github-personal-access-token"></a>创建 GitHub 个人访问令牌

若要在向 Git 存储库提交内容时触发任务，ACR 任务需要用于访问存储库的个人访问令牌 (PAT)。 如果还没有 PAT，请按照以下步骤在 GitHub 中生成一个：

1. 导航到 GitHub 上的 PAT 创建页面 https://github.com/settings/tokens/new
1. 输入令牌的简短**说明**，例如“ACR 任务演示”
1. 在“存储库”下方，启用“存储库:状态”和“public_repo”   

   ![GitHub 中个人访问令牌生成页面的屏幕截图][build-task-01-new-token]

1. 选择“生成令牌”按钮（可能会要求你确认密码） 
1. 将生成的令牌复制并保存到**安全位置**（在后续部分定义任务时会使用此令牌）

   ![GitHub 中已生成的个人访问令牌的屏幕截图][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
