## <a name="build-iot-edge"></a>生成 IoT 边缘

本教程使用自定义 IoT 边缘模块与远程监视的预配置解决方案进行通信。 因此，你需要构建自定义的源代码中的代码模块的 IoT 边缘。 下列各节描述如何安装 IoT 边缘和生成自定义的 IoT 边缘模块。

### <a name="install-iot-edge"></a>安装 IoT 边缘

以下步骤描述如何在 Intel NUC 上安装的预编译的 IoT 边缘软件：

1. 通过在 Intel NUC 上运行以下命令配置所需的智能包存储库：

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    输入`y`时该命令将提示你**包括此通道？**。

1. 通过运行以下命令更新智能包管理器：

    ```bash
    smart update
    ```

1. 通过运行以下命令安装 Azure IoT 边缘包：

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. 通过运行"Hello world"示例来验证安装。 此示例将你好 world 消息写入 log.txT 文件每五秒。 以下命令运行"Hello world"示例：

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    忽略任何**无效自变量**邮件时停止该示例。

    使用以下命令查看日志文件的内容：

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>疑难解答

如果你将收到错误"没有包提供了适用于开发人员 util linux"，请尝试重新 Intel NUC。
