## <a name="set-up-azure-cli-for-azure-dns"></a>为 Azure DNS 设置 Azure CLI

### <a name="before-you-begin"></a>在开始之前

验证你具有以下各项，在开始配置之前。

* Azure 订阅。 如果你还没有 Azure 订阅，则可以激活你[MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 安装最新版本的 Azure CLI，可用于 Windows、 Linux 或 mac。 详细信息位于[安装 Azure CLI](../articles/cli-install-nodejs.md)。

### <a name="sign-in-to-your-azure-account"></a>登录到你的 Azure 帐户

打开控制台窗口并使用你的凭据进行身份验证。 有关详细信息，请参阅[从 Azure CLI 登录到 Azure](../articles/xplat-cli-connect.md)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>交换机 CLI 模式

Azure DNS 使用 Azure 资源管理器。 请确保你 CLI 模式切换为使用 Azure 资源管理器命令。

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>选择的订阅

检查该帐户的订阅。

```azurecli
azure account list
```

选择你要使用的 Azure 订阅。

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>创建资源组

Azure 资源管理器要求所有资源组都指定的位置。 这是该资源组中的资源使用的默认位置。 但是，因为所有的 DNS 资源都是全局性而不是区域性，资源组位置的选择对 Azure DNS 没有任何影响。

如果你正在使用现有的资源组，则可以跳过此步骤。

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>注册资源提供程序

Azure DNS 服务由 Microsoft.Network 资源提供程序管理。 必须注册你的 Azure 订阅，可使用此资源提供程序，然后你可以使用 Azure DNS。 这是一次性操作为每个订阅。

```azurecli
azure provider register --namespace Microsoft.Network
```

