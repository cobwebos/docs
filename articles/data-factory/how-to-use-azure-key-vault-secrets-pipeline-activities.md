---
title: 在管道活动中使用 Azure Key Vault 机密
description: 了解如何从 Azure 密钥保管库提取存储的凭据，并在数据工厂管道运行期间使用这些凭据。
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 837d62784a56ad0f17471cca5a660819d4a83e12
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926763"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>在管道活动中使用 Azure Key Vault 机密

可以在 Azure Key Vault 中存储凭据或机密值，并在管道执行过程中将其用于传递到活动。

## <a name="prerequisites"></a>必备组件

此功能依赖于数据工厂托管的标识。  了解它如何从[用于数据工厂的托管标识](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)进行工作，并确保你的数据工厂有一个关联的。

## <a name="steps"></a>Steps

1. 打开数据工厂的属性，然后复制 "托管标识应用程序 ID" 值。

    ![托管标识值](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. 打开密钥保管库访问策略，并添加托管标识权限以获取和列出机密。

    ![Key Vault 访问策略](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault 访问策略](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    单击 "**添加**"，然后单击 "**保存**"。

3. 导航到 Key Vault 机密并复制机密标识符。

    ![机密标识符](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    记下要在数据工厂管道运行期间获取的机密 URI。

4. 在数据工厂管道中，添加新的 Web 活动，并按如下所示对其进行配置。  

    |properties  |Value  |
    |---------|---------|
    |安全输出     |正确         |
    |URL     |[机密 URI 值]？ api 版本 = 7。0         |
    |方法     |GET         |
    |Authentication     |MSI         |
    |资源        |https://vault.azure.net       |

    ![Web 活动](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > 必须将 **？ api 版本 = 7.0**添加到机密 URI 的末尾。  

    > [!CAUTION]
    > 将 Secure Output 选项设置为 true，以防止机密值以纯文本格式记录。  使用此值的任何其他活动都应将其 Secure Input 选项设置为 true。

5. 若要使用另一个活动中的值，请使用以下代码表达式 **@activity（"web"）。**

    ![代码表达式](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>后续步骤

若要了解如何使用 Azure Key Vault 存储数据存储和计算的凭据，请参阅[在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
