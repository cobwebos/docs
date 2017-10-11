## <a name="os-config"></a>将 IP 地址添加到 VM 的操作系统

连接并使用多个专用 IP 创建的 VM 的登录地址。 你必须手动添加所有 （包括主） 的专用 IP 地址添加到 VM。 完成 VM 操作系统的以下步骤：

### <a name="windows"></a>Windows

1. 在命令提示符，键入*ipconfig /all*。  你只看到*主*（通过 DHCP 分配) 的专用 IP 地址。
2. 类型*ncpa.cpl*在命令提示符处，以打开**网络连接**窗口。
3. 打开相应的适配器的属性：**本地区域连接**。
4. 双击 Internet 协议版本 4 (IPv4)。
5. 选择**使用下面的 IP 地址**并输入以下值：

    * **IP 地址**： 输入*主*专用 IP 地址
    * **子网掩码**： 集，基于你的子网。 例如，如果子网为/24 子网然后子网掩码为 255.255.255.0。
    * **默认网关**： 子网中的第一个 IP 地址。 如果你的子网，10.0.0.0/24 网关 IP 地址是 10.0.0.1。
    * 单击**使用以下 DNS 服务器地址**并输入以下值：
        * **首选的 DNS 服务器**： 如果你不使用你自己的 DNS 服务器，输入 168.63.129.16。  如果使用你自己的 DNS 服务器，输入你的服务器的 IP 地址。
    * 单击**高级**按钮，然后添加其他 IP 地址。 添加每个与主 IP 地址指定的同一子网的 nic 的步骤 8 中列出的辅助专用 IP 地址。
        >[!WARNING] 
        >如果不正确遵循上述步骤，你可能会失去连接到你的 VM。 确保在继续之前准确输入对于第 5 步的信息。

    * 单击**确定**关闭扩展 TCP/IP 设置，然后**确定**以关闭的适配器设置。 重新建立 RDP 连接。

6. 在命令提示符，键入*ipconfig /all*。 显示您添加的所有 IP 地址，并且 DHCP 已关闭。


### <a name="validation-windows"></a>验证 (Windows)

若要确保你能够从辅助 IP 连接到 internet 通过公共 IP 配置关联它，在已经将添加它正确使用上述步骤，使用以下命令：

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>对于辅助 IP 配置，你可以仅 ping 到 Internet 配置是否与它关联的公共 IP 地址。 对于主 IP 配置，不需要 ping 到 Internet 的公共 IP 地址。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. 打开终端窗口。
2. 请确保你是根用户。 如果你不受支持，输入以下命令：

    ```bash
    sudo -i
    ```

3. 更新 （假设 eth0） 的网络接口的配置文件。

    * 请为 dhcp 提供的现有行项。 以前一样，可以配置的主 IP 地址。
    * 添加其他的静态 IP 地址使用以下命令配置：

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    你应看到.cfg 文件。
4. 打开文件。 你应看到在文件末尾的以下行：

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. 在此文件中存在的行后添加以下行：

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    netmask <your subnet mask>
    ```

6. 通过使用以下命令保存文件：

    ```bash
    :wq
    ```

7. 重置网络接口使用以下命令：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > 如果使用远程连接在同一行中运行 ifdown 和 ifup。
    >

8. 验证 IP 地址添加到网络接口使用以下命令：

    ```bash
    ip addr list eth0
    ```

    你应看到作为列表的一部分添加的 IP 地址。

### <a name="linux-redhat-centos-and-others"></a>Linux （Redhat、 CentOS 和其他人）

1. 打开终端窗口。
2. 请确保你是根用户。 如果你不受支持，输入以下命令：

    ```bash
    sudo -i
    ```

3. 输入你的密码，然后按照说明进行操作，根据提示。 进入根用户后，导航到网络脚本文件夹使用以下命令：

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 列出相关的 ifcfg 文件使用以下命令：

    ```bash
    ls ifcfg-*
    ```

    你应该会看到*ifcfg-eth0*作为其中一个文件。

5. 若要添加 IP 地址，请为其创建配置文件如下所示。 请注意必须为每个 IP 配置创建该文件。

    ```bash
    touch ifcfg-eth0:0
    ```

6. 打开*ifcfg-eth0:0*文件使用以下命令：

    ```bash
    vi ifcfg-eth0:0
    ```

7. 将内容添加到文件， *eth0:0*在这种情况下，使用以下命令。 请务必更新基于你的 IP 地址的信息。

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. 使用以下命令保存该文件：

    ```bash
    :wq
    ```

9. 重新启动的网络服务，并确保所做的更改都成功通过运行以下命令：

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    您应该看到你添加的 IP 地址*eth0:0*，返回的列表中。

### <a name="validation-linux"></a>验证 (Linux)

若要确保你能够连接到 internet 从辅助 IP 配置通过公共 IP 关联它，请使用以下命令：

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>对于辅助 IP 配置，你可以仅 ping 到 Internet 配置是否与它关联的公共 IP 地址。 对于主 IP 配置，不需要 ping 到 Internet 的公共 IP 地址。

对于 Linux Vm，尝试验证从辅助 NIC 的出站连接时，你可能需要添加适当的路由。 有多种方法来执行此操作。 请参阅针对 Linux 分发的相应文档。 下面是实现此目的的一种方法：

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- 请务必将：
    - **10.0.0.5**具有与其关联的一个公共 IP 地址的专用 IP 地址
    - **10.0.0.1**到默认网关
    - **eth2**辅助 NIC 的名称
