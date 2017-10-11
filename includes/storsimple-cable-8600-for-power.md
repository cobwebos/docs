<!--author=alkohli last changed: 9/16/15-->


#### <a name="to-cable-your-device-for-power"></a>若要对设备进行电源布线
> [!NOTE]
> StorSimple 设备上的这两个机箱中都包括冗余的 Pcm。 对于每个机箱中，必须安装并连接到不同电源，以确保高可用性的 Pcm。
> 
> 

1. 请确保所有 Pcm 上的电源开关处于关位置。
2. 主机箱中，将连接到两个 Pcm 的电源线连接。 以下情况下，将电源线标识中 power 缆线连接图，以红色。
3. 请确保主机箱上的两个 Pcm 使用单独的电源。
4. 中电源布线关系图所示，请将电源线连接附加到机架配电装置上的电源。
5. 对于 EBOD 机箱，重复步骤 2 至 4。
6. 切换到开位置的每个 PCM 上的电源开关以打开 EBOD 机箱。
7. 验证 EBOD 机箱打开通过检查 EBOD 控制器背面的绿色 Led 已打开。
8. 每个 PCM 开关翻转到开位置开打开主机箱。
9. 请验证系统上方法是确保设备控制器 Led 已打开。
10. 确保 EBOD 控制器和设备控制器之间的连接通过验证 EBOD 控制器上 SAS 端口旁的 4 个 Led 为绿色中处于活动状态。
    
    > [!IMPORTANT]
    > 若要确保你的系统的高可用性，我们建议你严格遵循的电源布线方案下图中所示。
    > 
    > 
    
    ![你将 4U 设备进行电源布线](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **电源布线**
    
    | Label | 描述 |
    |:--- |:--- |
    | 1 |主机箱 |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |控制器 0 |
    | 5 |控制器 1 |
    | 6 |EBOD 控制器 0 |
    | 7 |EBOD 控制器 1 |
    | 8 |EBOD 机箱 |
    | 9 |Pdu |

