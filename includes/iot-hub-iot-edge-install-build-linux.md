## <a name="install-the-prerequisites"></a>安装必备组件

本教程中的这些步骤假设运行的是 Ubuntu Linux。

要安装必备组件包，请打开 shell 并运行以下命令：

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

在 shell 中，运行以下命令，将 Azure IoT Edge GitHub 存储库克隆到本地计算机上：

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>如何生成示例

现在可在本地计算机上生成 IoT Edge 运行时和示例：

1. 打开 shell。

1. 浏览到 **iot-edge** 存储库本地副本中的根文件夹。

1. 如下所示运行生成脚本：

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

此脚本使用 **cmake** 实用工具在 **iot-edge** 存储库本地副本的根文件夹中创建一个名为 **build** 的文件夹，并生成一个生成文件。 然后，该脚本将生成解决方案并跳过单元测试和端到端测试。 如果想要生成并运行单元测试，请添加 `--run-unittests` 参数。 如果想要生成并运行端到端测试，请添加 `--run-e2e-tests`。

> [!NOTE]
> 每次运行 **build.sh** 脚本时，都会删除 **iot-edge** 存储库本地副本的根文件夹中的 **build** 文件夹并重新生成。