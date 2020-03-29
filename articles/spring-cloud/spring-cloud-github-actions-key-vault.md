---
title: 在 GitHub 操作中使用密钥保管库对 Azure 春云进行身份验证
description: 如何使用密钥保管库与 CI/CD 工作流一起使用 Azure 春云操作
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945474"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>在 GitHub 操作中使用密钥保管库对 Azure 春云进行身份验证
密钥保管库是存储密钥的安全位置。 企业用户需要将 CI/CD 环境的凭据存储在他们控制的范围内。 在密钥保管库中获取凭据的键应限于资源范围。  它只能访问密钥保管库作用域，而不是整个 Azure 作用域。 它就像一个钥匙，只能打开一个强大的盒子，而不是主钥匙，可以打开所有的门在建筑物。 这是一种使用另一个密钥获取密钥的方法，这在 CICD 工作流中非常有用。 

## <a name="generate-credential"></a>生成凭据
要生成密钥以访问密钥保管库，请在本地计算机上执行以下命令：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
`--scopes`参数指定的作用域限制对资源的密钥访问。  它只能访问强框。

结果：
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
然后，将结果保存到 GitHub**机密**，如[设置 GitHub 存储库中所述，并使用 Azure 进行身份验证](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)。

## <a name="add-access-policies-for-the-credential"></a>添加凭据的访问策略
上面创建的凭据只能获取有关密钥保管库的一般信息，而不能获取它存储的内容。  要获取存储在密钥保管库中的机密，您需要为凭据设置访问策略。

转到 Azure**scope**门户中的 **"密钥保管库**"仪表板，单击 **"访问控制"** 菜单，然后打开**角色分配**选项卡。 **Type** `This resource` **Apps**  您应该会看到您在上一步中创建的凭据：

 ![设置访问策略](./media/github-actions/key-vault1.png)

复制凭据名称，例如。 `azure-cli-2020-01-19-04-39-02` 打开 **"访问策略"** 菜单，单击 **"添加访问策略"** 链接。  为`Secret Management`**模板**选择 ，然后选择**主体**。 将凭据名称粘贴在**主体**/**选择**输入框中：

 ![Select](./media/github-actions/key-vault2.png)

 单击"**添加访问策略"** 对话框中的 **"添加**"按钮，然后单击"**保存**"。

## <a name="generate-full-scope-azure-credential"></a>生成全范围 Azure 凭据
这是打开大楼所有门的主钥匙。 该过程与上一步类似，但在这里我们更改范围以生成主密钥：

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

同样，结果：
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
复制整个 JSON 字符串。  Bo 返回**密钥保管库**仪表板。 打开 **"机密"** 菜单，然后单击 **"生成/导入**"按钮。 输入机密名称，如`AZURE-CRENDENTIALS-FOR-SPRING`。 将 JSON 凭据字符串粘贴到 **"值"** 输入框。 您可能会注意到值输入框是一行文本字段，而不是多行文本区域。  您可以在此处粘贴完整的 JSON 字符串。

 ![全范围凭据](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>在 GitHub 操作中合并凭据
设置执行 CICD 管道时使用的凭据：

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>后续步骤
* [春云 GitHub 操作](./spring-cloud-howto-github-actions.md)