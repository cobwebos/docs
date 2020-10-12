---
title: 在 Azure Red Hat OpenShift 4 群集上添加或更新 Red Hat 请求机密
description: 在现有的 4.x ARO 群集上添加或更新 Red Hat 请求机密
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: pull secret，aro，openshift，red hat
ms.openlocfilehash: 769b7589fb6496fc2f4123665ad1f6fe61d0cce2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294741"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上添加或更新 Red Hat 请求机密

本指南介绍了如何为现有的 Azure Red Hat OpenShift (ARO) 4.x 群集添加或更新 Red Hat 请求机密。

如果是首次创建群集，则可以在创建群集时添加拉取机密。 有关使用 Red Hat 请求机密创建 ARO 群集的详细信息，请参阅 [创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)。

## <a name="before-you-begin"></a>在开始之前

本指南假定你已有一个 Azure Red Hat OpenShift 4 群集。 确保你有权访问群集。

## <a name="prepare-your-pull-secret"></a>准备你的请求机密
如果在不添加 Red Hat 请求机密的情况下创建了 ARO 群集，则仍会自动在群集上创建请求密钥。 但是，此请求密码并未完全填充。

本部分介绍如何使用 Red Hat 请求机密中的其他值更新该请求机密。

1. `pull-secret` `openshift-config` 通过运行以下命令，提取命名空间中名为的机密，并将其保存到单独的文件中： 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    输出应如下所示。  (请注意，实际的机密值已删除。 ) 

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. 中转到 [Red Hat OpenShift 群集管理器门户](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) ，并选择 " **下载请求机密**"。 Red Hat 请求机密将如下所示。  (请注意，实际机密值已删除。 ) 

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. 通过添加在你的 Red Hat 请求机密中找到的条目，编辑你从群集中获取的 pull 机密文件。 

    > [!IMPORTANT]
    > 包含 `cloud.openshift.com` Red hat pull 机密中的条目将导致群集开始向 Red hat 发送遥测数据。 仅当要发送遥测数据时，才包括此部分。 否则，请将以下部分留空。    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > 不要删除或更改你的 `arosvc.azurecr.io` 请求机密中的条目。 此部分是群集正常运行所必需的。

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    最终文件应如下所示。  (请注意，实际机密值已删除。 ) 

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. 确保该文件是有效的 JSON。 可以通过多种方式来验证 JSON。 下面的示例使用 jq：

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > 如果文件中有错误，则显示为 `parse error` 。

## <a name="add-your-pull-secret-to-your-cluster"></a>将拉取机密添加到群集

运行以下命令以更新你的请求密钥。

> [!NOTE]
> 运行此命令会导致群集节点在更新时重新启动。 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

设置密码后，即可启用 Red Hat 认证的操作员。

### <a name="modify-the-configuration-files"></a>修改配置文件

修改下列对象以启用 Red Hat 运算符。

首先，修改 Samples 运算符配置文件。 然后，你可以运行以下命令来编辑配置文件：

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

将 `spec.architectures.managementState` 和 `status.architecture.managementState` 值从更改 `Removed` 为 `Managed` 。 

以下 YAML 代码片段只显示已编辑的 YAML 文件的相关部分：

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

然后，运行以下命令来编辑操作员中心配置文件：  

```console
oc edit operatorhub cluster -o yaml
```

将 `Spec.Sources.Disabled` 和的 `Status.Sources.Disabled` 值从更改 `true` 为 `false` 要启用的任何源。

以下 YAML 代码片段只显示已编辑的 YAML 文件的相关部分：

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

保存文件以应用所做的编辑。

## <a name="validate-that-your-secret-is-working"></a>验证机密是否正常工作

添加你的请求机密并修改正确的配置文件后，你的群集可能需要几分钟才能更新。 若要检查群集是否已更新，请运行以下命令以显示可用的经过认证的操作员和 Red Hat 操作员源：

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

如果看不到已认证的操作员和 Red Hat 运算符，请等待几分钟，然后重试。

若要确保你的请求机密已更新并且工作正常，请打开 OperatorHub 并检查是否有任何 Red Hat 验证的操作员。 例如，查看 OpenShift 容器存储操作员是否可用，并查看是否有权安装。

## <a name="next-steps"></a>后续步骤
若要详细了解 Red Hat 请求机密，请参阅 [使用映像请求密码](https://docs.openshift.com/container-platform/4.5/openshift_images/managing_images/using-image-pull-secrets.html)。

若要详细了解 Red Hat OpenShift 4，请参阅 [Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/welcome/index.html)。
