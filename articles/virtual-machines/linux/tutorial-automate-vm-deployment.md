---
title: 教程 - 在 Azure 中使用 cloud-init 自定义 Linux VM
description: 本教程介绍在 Azure 中首次启动 Linux VM 时如何使用 cloud-init 和 Key Vault 对其进行自定义
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d2a6568b0d62c880a688160cf981fb33083ae02e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81461474"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>教程 - 如何在 Azure 中的 Linux 虚拟机首次启动时使用 cloud-init 对其进行自定义

在前面的教程中，已学习如何通过 SSH 连接到虚拟机 (VM) 并手动安装 NGINX。 若要以快速一致的方式创建 VM，通常需要某种形式的自动化。 在首次启动 VM 时实现自定义的常见方法是使用 [cloud-init](https://cloudinit.readthedocs.io)。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 cloud-init 配置文件
> * 创建使用 cloud-init 文件的 VM
> * 在创建 VM 后，查看正在运行的 Node.js 应用
> * 使用 Key Vault 安全地存储证书
> * 使用 cloud-init 自动进行 NGINX 的安全部署

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="cloud-init-overview"></a>Cloud-init 概述
[Cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 在初始启动期间运行 cloud-init 时，无需额外的步骤且无需代理来应用配置。

Cloud-init 还支持不同的分发。 例如，不要使用 apt-get 安装  或 yum 安装  来安装包。 可定义要安装的程序包的列表。 Cloud-init 将为所选发行版自动使用本机包管理工具。

我们正在与合作伙伴协作，将 cloud-init 纳入用户向 Azure 提供的映像中并使其在映像中正常运行。 下表概述了 cloud-init 当前在 Azure 平台映像上的可用性：

| 发布者 | 产品/服务 | SKU | 版本 | cloud-init 就绪 |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |最新 |是 | 
|Canonical |UbuntuServer |16.04-LTS |最新 |是 | 
|Canonical |UbuntuServer |14.04.5-LTS |最新 |是 |
|CoreOS |CoreOS |Stable |最新 |是 |
|OpenLogic 7.6 |CentOS |7-CI |最新 |预览 |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 |
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 |预览 |


## <a name="create-cloud-init-config-file"></a>创建 cloud-init 配置文件
要运行 cloud-init，请创建一个 VM，该 VM 将安装 NGINX 并运行简单的“Hello World”Node.js 应用。 以下 cloud-init 配置会安装所需的程序包、创建 Node.js 应用，然后初始化并启动该应用。

在 bash 提示符下或在 Cloud Shell 中，创建名为 cloud-init.txt  的文件并粘贴以下配置。 例如，键入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```bash
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

有关 cloud-init 配置选项的详细信息，请参阅 [cloud-init 配置示例](https://cloudinit.readthedocs.io/en/latest/topics/examples.html)。

## <a name="create-virtual-machine"></a>创建虚拟机
使用 [az group create](/cli/azure/group#az-group-create) 创建资源组，才能创建 VM。 以下示例在“eastus”位置创建名为“myResourceGroupAutomate”的资源组：

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

现使用 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM。 使用 `--custom-data` 参数传递到 cloud-init 配置文件中。 如果未将 cloud-init.txt  配置文件保存在现有工作目录中，请提供该文件的完整路径。 以下示例创建一个名为 *myVM* 的 VM：

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

创建 VM、安装程序包和启动应用需耗时几分钟。 在 Azure CLI 向你返回提示之后，仍然存在继续运行的后台任务。 可能还需等待几分钟才能访问应用。 创建 VM 后，请记下 Azure CLI 显示的 `publicIpAddress`。 此地址用于通过 Web 浏览器访问 Node.js 应用。

若要使 VM 能使用 Web 流量，请通过 [az vm open-port](/cli/azure/vm#az-vm-open-port) 从 Internet 中打开端口 80：

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>测试 Web 应用
现在可以打开 Web 浏览器，并在地址栏中输入“http:\/\/\<publicIpAddress>”。 在 VM 创建过程中提供自己的公共 IP 地址。 将显示 Node.js 应用，如下例所示：

![查看运行中的 NGINX 站点](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>插入 Key Vault 中的证书
此可选部分展示了如何在 Azure Key Vault 中安全存储证书，并在 VM 部署期间将其插入。 此过程可确保首次启动时会最新的证书插入到 VM 中，而不是使用内置证书中随附的自定义映像。 在该过程中，证书永远不会离开 Azure 平台，也不会在脚本、命令行历史记录或模板中公开。

Azure Key Vault 保护加密密钥和机密，例如证书或密码。 Key Vault 有助于简化密钥管理过程，让你能够持续掌控用于数据访问和加密的密钥。 此方案介绍了一些用于证书创建和使用的 Key Vault 概念，但未详尽概述如何使用 Key Vault。

以下步骤演示可如何：

- 创建 Azure Key Vault
- 生成证书或将其上传到 Key Vault
- 利用要插入到 VM 的证书创建密钥
- 创建 VM 并插入证书

### <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault
首先，使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create) 创建 Key Vault，并在部署 VM 时启用该 Key Vault。 每个 Key Vault 均需具备唯一名称且全部小写。 将下例中的 mykeyvault  替换为自己唯一的 Key Vault 名称：

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>生成证书并存储在 Key Vault 中
为供生产使用，应通过 [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) 导入由受信任的提供程序签名的有效证书。 在本教程中，以下示例显示了如何使用 [az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) 生成使用默认证书策略的自签名证书：

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>准备用于 VM 的证书
若要在 VM 创建过程中使用该证书，请使用 [az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions) 获取证书的 ID。 VM 需要特定格式的证书才能在启动时将其注入，因此请使用 [az vm secret format](/cli/azure/vm) 转换该证书。 以下示例将这些命令的输出分配给变量，以便在后续步骤中使用：

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>创建 cloud-init 配置以保护 NGINX
创建 VM 时，证书和密钥都将存储在受保护的 /var/lib/waagent/  目录中。 要将证书自动添加到 VM 并配置 NGINX，可使用上一示例中的已更新的 cloud-init 配置。

创建名为“cloud-init-secured.txt”  的文件并粘贴下面的配置。 如果使用 Cloud Shell，则在该处而非本地计算机上创建 cloud-init 配置文件。 例如，键入 `sensible-editor cloud-init-secured.txt` 以创建文件并查看可用编辑器的列表。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>创建安全的 VM
现使用 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM。 使用 `--secrets` 参数插入 Key Vault 中的证书数据。 与上个示例一样，使用 `--custom-data` 参数传递到 cloud-init 配置中：

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

创建 VM、安装程序包和启动应用需耗时几分钟。 在 Azure CLI 向你返回提示之后，仍然存在继续运行的后台任务。 可能还需等待几分钟才能访问应用。 创建 VM 后，请记下 Azure CLI 显示的 `publicIpAddress`。 此地址用于通过 Web 浏览器访问 Node.js 应用。

若要使 VM 能使用安全的 Web 流量，请通过 [az vm open-port](/cli/azure/vm#az-vm-open-port) 从 Internet 中打开端口 443：

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>测试 Web 应用是否安全
现在可以打开 Web 浏览器，并在地址栏中输入“https:\/\/\<publicIpAddress>”。 提供你自己的公共 IP 地址，如前面的 VM 创建过程的输出中所示。 若使用自签名的证书，请接受安全警告：

![接受 Web 浏览器安全警告](./media/tutorial-automate-vm-deployment/browser-warning.png)

随即显示受保护的 NGINX 站点和 Node.js 应用，如下例所示：

![查看运行中的安全 NGINX 站点](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>后续步骤
在本教程中，使用 cloud-init 在首次启动时配置 VM。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 cloud-init 配置文件
> * 创建使用 cloud-init 文件的 VM
> * 在创建 VM 后，查看正在运行的 Node.js 应用
> * 使用 Key Vault 安全地存储证书
> * 使用 cloud-init 自动进行 NGINX 的安全部署

转到下一教程，了解如何创建自定义 VM 映像。

> [!div class="nextstepaction"]
> [创建自定义 VM 映像](./tutorial-custom-images.md)
