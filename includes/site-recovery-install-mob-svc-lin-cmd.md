1. 将安装程序复制到要保护的服务器上的某个本地文件夹（如 /tmp），然后在终端窗口中运行以下命令。
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. 现在，可使用以下命令行安装移动服务

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>移动服务安装程序命令行参数

|参数|类型|说明|可能的值|
|-|-|-|-|
|-t |必需|代理类型<br>（将在下一个版本中弃用）|*both*|
|-a |必需|代理配置<br>（将在下一个版本中弃用） |*主机*|
|-R |可选|代理角色|代理<br>MasterTarget|
|-d |可选|移动服务要安装到的位置|/usr/local/ASR|
|-i |必需|配置服务器的 IP 地址。|任何有效的 IP 地址|
|-p |必需|配置服务器侦听传入连接时所在的端口|443|
|-s |必需|成功安装后启动服务<br>（将在下一个版本中弃用）|*y*|
|-c |必需|代理与进程服务器之间的通信模式<br>（将在下一个版本中弃用） |*https*|
|-P |必需|配置服务器通行短语|任何有效的 UNC 或本地文件路径|


#### <a name="sample-usage"></a>示例用法
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```


<!--HONumber=Jan17_HO3-->


