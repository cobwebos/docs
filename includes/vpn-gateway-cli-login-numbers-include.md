1. 登录到你的 Azure 订阅与[az 登录](/cli/azure/#login)命令并按照屏幕上的说明。 有关日志记录的详细信息，请参阅[开始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)。

  ```azurecli
  az login
  ```
2. 如果你有多个 Azure 订阅，列出的帐户的订阅。

  ```azurecli
  az account list --all
  ```
3. 指定你想要使用的订阅。

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```