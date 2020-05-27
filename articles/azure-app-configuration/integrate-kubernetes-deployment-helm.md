---
title: 使用 Helm 将 Azure 应用程序配置与 Kubernetes 部署相集成
description: 了解如何通过 Helm 在 Kubernetes 部署中使用动态配置。
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: aac42e6f782ac1e939ff955c5811238f99e703eb
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725663"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>使用 Helm 来与 Kubernetes 部署集成

使用 Helm 可以定义、安装和升级 Kubernetes 中运行的应用程序。 Helm 图表包含创建 Kubernetes 应用程序实例所需的信息。 配置存储在图表本身外部的名为 values.yaml 的文件中  。 

在发布过程中，Helm 会将图表与正确的配置合并，以运行应用程序。 例如，可将 values.yaml 中定义的变量作为正在运行的容器内部的环境变量进行引用  。 Helm 还支持创建 Kubernetes 机密，可将这些机密装载为数据卷，或公开为环境变量。

在运行 Helm 时，可以通过在命令行中提供其他基于 YAML 的配置文件来重写 values.yaml 中存储的值  。 Azure 应用程序配置支持将配置值导出到 YAML 文件。 将此导出功能集成到部署中可让 Kubernetes 应用程序利用应用程序配置中存储的配置值。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 在使用 Helm 将应用程序部署到 Kubernetes 时使用应用程序配置中的值。
> * 基于应用程序配置中的 Key Vault 参考创建 Kubernetes 机密。

本教程假定读者基本了解如何使用 Helm 管理 Kubernetes。 在 [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/kubernetes-helm)中详细了解如何使用 Helm 安装应用程序。

## <a name="prerequisites"></a>先决条件

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- 安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)（2.4.0 或更高版本）
- 安装 [Helm](https://helm.sh/docs/intro/install/)（2.14.0 或更高版本）
- 一个 Kubernetes 群集。

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 选择“配置资源管理器” > “创建”来添加以下键值对   ：

    | 密钥 | 值 |
    |---|---|
    | settings.color | 白色 |
    | settings.message | Azure 应用配置的数据 |

    暂时将“标签”和“内容类型”保留为空   。

## <a name="add-a-key-vault-reference-to-app-configuration"></a>将 Key Vault 引用添加到应用程序配置
1. 登录到 [Azure 门户](https://portal.azure.com)，将名称为 Password、值为 myPassword 的机密添加到 [Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)   。 
2. 选择在上一部分创建的应用程序存储实例。

3. 选择“配置资源管理器”。 

4. 选择“+ 创建” > “Key Vault 引用”，然后指定以下值：  
    - **密钥**：选择“secrets.password”  。
    - **标签**：将此值保留空白。
    - “订阅”、“资源组”和“Key Vault”：    输入上一步在 Key Vault 中创建的项相对应的值。
    - **机密**：选择在上一部分创建的名为 Password 的机密  。

## <a name="create-helm-chart"></a>创建 Helm 图表 ##
首先，使用以下命令创建一个示例 Helm 图表
```console
helm create mychart
```

Helm 将采用如下所示的结构创建名为 mychart 的一个新目录。 

> [!TIP]
> 在此[图表指南](https://helm.sh/docs/chart_template_guide/getting_started/)中了解更多信息。

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

接下来，更新 deployment.yaml 文件的 spec:template:spec:containers 节   。 以下代码片段将两个环境变量添加到容器。 在部署时，你将动态设置这些变量的值。

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

更新后的完整 deployment.yaml 文件应如下所示  。

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

若要将敏感数据存储为 Kubernetes 机密，请在 templates 文件夹下添加一个 secrets.yaml 文件  。

> [!TIP]
> 详细了解如何使用 [Kubernetes 机密](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)。

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

最后，使用以下内容更新 values.yaml 文件，以便选择性地为 deployment.yaml 和 secrets.yaml 文件中引用的配置设置和机密提供默认值    。 从应用程序配置中提取的配置将覆盖其实际值。

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>在 Helm 安装中传递应用程序配置中的配置 ##
首先，将应用程序配置中的配置下载到 myConfig.yaml 文件  。 使用密钥筛选器，以便仅下载以 settings. 开头的密钥  。 如果密钥筛选器不足以排除 Key Vault 引用的密钥，可以使用参数 --skip-keyvault 来排除它们  。 

> [!TIP]
> 详细了解 [export 命令](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export)。 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

接下来，将机密下载到名为 mySecrets.yaml 的文件  。 命令行参数 --resolve-keyvault 通过检索 Key Vault 中的实际值来解析 Key Vault 参考  。 需要使用对相应 Key Vault 拥有访问权限的凭据来运行此命令。

> [!WARNING]
> 由于此文件包含敏感信息，因此请谨慎保存此文件，并在不再需要时清理其内容。

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

使用 helm upgrade 的 -f 参数传入你创建的两个配置文件  。 这些文件将使用从应用程序配置导出的值重写 values.yaml 中定义的配置值  。

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

还可以使用 --set 参数让 helm upgrade 传递文本密钥值  。 使用 --set 参数能够很好地避免在磁盘中保存敏感数据  。 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

访问 [Kubernetes 仪表板](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)来验证是否已成功设置配置和机密。 你将看到，应用程序配置中的 color 和 message 值已填充到容器的环境变量中   。

![本地启动应用快速入门](./media/kubernetes-dashboard-env-variables.png)

此外，已将一个在应用程序配置中作为 Key Vault 参考存储的机密 password 添加到了 Kubernetes 机密  。 

![本地启动应用快速入门](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已通过 Helm 导出了要在 Kubernetes 部署中使用的 Azure 应用程序配置数据。 若要了解有关如何使用应用程序配置的更多信息，请继续阅读 Azure CLI 示例。

> [!div class="nextstepaction"]
> [Azure CLI](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
