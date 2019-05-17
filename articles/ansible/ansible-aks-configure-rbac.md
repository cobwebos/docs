---
title: 教程 - 使用 Ansible 在 Azure Kubernetes 服务 (AKS) 中配置基于角色的访问控制 (RBAC) 角色 | Microsoft Docs
description: 了解如何使用 Ansible 在 Azure Kubernetes 服务 (AKS) 群集中配置 RBAC
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, 容器, aks, Kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: dbef7c2cb8de5a1b4bbb3073f694b8f77c9f441b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231295"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>教程：使用 Ansible 在 Azure Kubernetes 服务 (AKS) 中配置基于角色的访问控制 (RBAC) 角色

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

可将 AKS 配置为使用 [Azure Active Directory (AD)](/azure/active-directory/) 进行用户身份验证。 配置后，使用 Azure AD 身份验证令牌登录到 AKS 群集。 RBAC 可基于用户标识或目录组成员身份。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 创建支持 Azure AD 的 AKS 群集
> * 在群集中配置 RBAC 角色

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **安装 RedHat OpenShift 库** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>配置 Azure AD 以进行 AKS 身份验证

若要配置 Azure AD 以进行 AKS 身份验证，需配置两个 Azure AD 应用程序。 此操作必须由 Azure 租户管理员完成。 有关详细信息，请参阅[将 Azure Active Directory 与 AKS 集成](/azure/aks/aad-integration#create-server-application)。 

通过 Azure 租户管理员获取以下内容：

- 服务器应用密码
- 服务器应用 ID
- 客户端应用 ID 
- 租户 ID

需要这些值来运行示例 playbook。  

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

在本部分中，将通过 [Azure AD 应用程序](#configure-azure-ad-for-aks-authentication)创建 AKS。

下面是使用示例 playbook 时要考虑的部分关键注意事项：

- playbook 通过 `~/.ssh/id_rsa.pub` 加载 `ssh_key`。 若要修改，使用以“ssh-rsa”开头（不带引号）的单行格式。
- `client_id` 和 `client_secret` 值通过默认的凭据文件 `~/.azure/credentials` 进行加载。 可将这些值设置为服务主体，也可通过环境变量加载：

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

将以下 playbook 保存为 `aks-create.yml`：

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>获取 Azure AD 对象 ID

要创建 RBAC 绑定，首先需要获取 Azure AD 对象 ID。 

1. 登录到 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 在页面顶部的搜索字段中，输入 `Azure Active Directory`。 

1. 单击`Enter`。

1. 在“管理”菜单中选择“用户”。

1. 在名称字段中，搜索帐户。

1. 在“名称”列中，选择帐户链接。

1. 在“标识”部分，复制“对象 ID”。

    ![复制 Azure AD 对象 ID。](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>创建 RBAC 绑定

在本部分中，将在 AKS 中创建角色绑定或群集角色绑定。 

将以下 playbook 保存为 `kube-role.yml`：

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

使用 Azure AD 租户[对象 ID](#get-the-azure-ad-object-id) 替换 `&lt;your-aad-account>` 占位符。

按 `aks-kube-deploy.yml` 形式保存下列将新角色部署到 AKS 的 playbook：

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>运行示例 playbook

本部分列出了用于调用本文所创任务的完整示例 playbook。 

将以下 playbook 保存为 `aks-rbac.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

在 `vars` 部分，使用 Azure AD 信息替换以下占位符：

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

使用 `ansible-playbook` 命令运行完整的 playbook：

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>验证结果

在本部分中，将使用 kubectl 列出本文中创建的节点。

在终端提示符处输入以下命令：

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

该命令将定向到身份验证页面。 使用 Azure 帐户登录。

身份验证后，kubectl 将按与下述结果的类似形式列出节点：

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。 

将以下代码保存为 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 上的 Ansible](/azure/ansible/)