---
title: "Unity 滚动球教程"
description: "创建经典 Unity 滚动球游戏的步骤，这是所有 Mobile Engagement Unity 教程中必不可少的部分"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a id="unity-roll-a-ball"></a>创建 Unity 回滚球游戏
本教程介绍了稍经修改的 [Unity 滚动球教程](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)的主要步骤。 此示例游戏包含一个“Player”球形对象（该对象由应用程序用户控制），而这款游戏的目标是通过将 Player 对象与可收集对象碰撞以“收集”这些可收集对象。 本教程假定用户基本熟悉 Unity 编辑器的环境。 如果遇到任何问题，请参考完整教程。 

### <a name="setting-up-the-game"></a>设置游戏
以下步骤来自 [Unity 教程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. 打开“Unity 编辑器”，并单击“新建”。 
   
    ![][51] 
2. 提供“项目名称” & “位置”，选择“3D”，并单击“创建项目”。
   
    ![][52]
3. 将刚才创建的默认场景另存为新项目的一部分（名为 **MiniGame**，位于 **Assets** 文件夹下新建的 **\_ Scenes** 文件夹中）：
   
    ![][53]
4. 创建**三维对象-> 平面**作为播放字段和重命名此平面对象作为**接地**
   
    ![][1]
5. 重置该 **Ground** 对象的转换组件，使其位于原点。 
   
    ![][3]
6. 从“Gizmos 菜单”为 **Ground** 对象取消选中“显示网格”。
   
    ![][4]
7. 将 **Ground** 对象的 **Scale** 组件更新至 [X = 2、Y = 1、 Z = 2]。 
   
    ![][5]
8. 添加新**三维对象-> 球**到的项目和重命名此球面对象作为**播放器**。 
   
    ![][6]
9. 选择 **Player** 对象并单击与 Plane 对象类似的“重置转换”。 
10. 更新**转换-> 位置-> Y 坐标**为 0.5 播放器 Y 分量。  
    
    ![][7]
11. 在项目中，新建名为“材质”的文件夹（我们会在此处对 Player 对象创建颜色材质）。 
12. 在此文件夹中，新建名为“背景”的“材质”。 
    
    ![][8]
13. 通过更新其 **Albedo** 属性，更新材质颜色。 可选择 [0,32,64] 的 RGB 值。 
    
    ![][9]
14. 将此材质拖动到场景视图，以向 **Ground** 对象应用颜色。 
    
    ![][10]
15. 最后更新**转换-> 旋转-> Y**到 60 为清楚起见定向光对象上。 
    
    ![][12]

### <a name="moving-the-player"></a>移动游戏球
以下步骤来自 [Unity 教程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. 将 **RigidBody** 组件添加到 **Player** 对象。 
   
    ![][13]
2. 在项目中，新建名为“脚本”的文件夹。 
3. 单击“添加组件-> 新建脚本 -> C# 脚本”。 将其命名为 **PlayerController**，并单击“创建和添加”。 该操作将创建一个脚本，并将其附加到 Player 对象。  
   
    ![][14]
4. 在项目的“**脚本**”文件夹下移动此脚本。 
5. 在常用的脚本编辑器中打开脚本进行编辑，使用以下代码更新脚本并保存。 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. 请注意，以上脚本使用“Speed”属性。 在 Unity 编辑器中，将 Speed 属性更新至 10。  
   
    ![][15]
7. 单击 Unity 编辑器中的“Play”。 现在，应能使用键盘控制小球，且小球应旋转并来回滚动。 

### <a name="moving-the-camera"></a>移动摄像头
以下步骤来自 [Unity 教程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141)，介绍如何将 **Main Camera** 与 **Player** 对象相关联。 

1. 将 **Transform.Position** 更新至 X = 0、Y = 10.5、Z=-10。  
2. 将 **Transform.Rotation** 更新至 X = 45、Y = 0、Z= 0。  
   
    ![][16]
3. 将名为 **CameraController** 的新脚本添加到 **MainCamera**，并将其移至“脚本”文件夹下。 
   
    ![][17]
4. 打开脚本进行编辑，并向其添加以下代码：
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. 在 Unity 环境中，将 Player 变量拖动到 Main Camera 对象的 Player 槽，以使二者相互关联。 
   
    ![][18]
6. 现在，如果单击 Unity 编辑器中的“Play”，并旋转 Player Ball 对象，将看到跟随该对象移动的摄像头。  

### <a name="setting-up-the-play-area"></a>设置 Play 区域
以下步骤来自 [Unity 教程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141)。 我们会在 Ground 周围创建 Walls，以免 Player Ball 对象在移动中跌落到游戏区域以外。 

1. 单击**创建-> 创建空白-> 游戏对象**并将其命名**墙**
   
    ![][19]
2. 在此墙对象-下创建一个新**三维对象-> 多维数据集**并将其命名为"西部 wall"。 
   
    ![][20]
3. 为该 West Wall 对象更新“转换 -> 位置”和“转换 -> 缩放”。 
   
    ![][21]
4. 重复 West Wall，以使用更新的转换位置和缩放比例创建 **East Wall**。 
   
    ![][22]
5. 重复 Eest Wall，以使用更新的转换位置和缩放比例创建 **North Wall**。 
   
    ![][23]
6. 重复 North Wall，以使用更新的转换位置和缩放比例创建 **South Wall**。 
   
    ![][24]

### <a name="creating-collectible-objects"></a>创建 Collectible 对象
以下步骤来自 [Unity 教程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141)。 我们将创建一些有意思的对象，这些对象将构成可收集对象集，而 Player Ball 对象需通过与其碰撞来“收集”这些对象。 

1. 创建新的 **3D Cube** 对象，并将其命名为 Pickup。 
2. 调整 Pickup 对象的“转换 -> 旋转” & “转换 -> 旋转”。 
   
    ![][25]
3. 创建名为 **Rotator** 的**新 C# 脚本**，并将其附加到 Pickup 对象。 请确保将脚本置于“脚本”文件夹下。 
   
    ![][26]
4. 打开该脚本进行编辑，并将其更新为： 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. 现在，单击 Unity 编辑器中的“Play”模式，Pickup 对象应在其轴上旋转。
6. 创建名为 **Prefabs** 的新文件夹。 
   
    ![][27]
7. 拖动 **Pickup** 对象并将其置入 Prefabs 文件夹中。
   
    ![][28]
8. 创建名为 **Pickups** 的**空游戏对象**。 将它的位置重置到原始位置，然后将 Pickup 对象拖动到该游戏对象下。  
   
    ![][29]
9. 重复**拾取**对象的限制，扩展上**接地**对象周围**播放器**通过更新对象**Transform.Position 的 X 和 Z**适当值。 
   
    ![][30]
10. 创建名为 **Pickup** 的**新材质**，并通过更新 **Albedo 属性**（与更新 Ground 对象的方法类似）将该材质更新为红色。 
    
    ![][31]
11. 将该材质应用于所有 4 个 Pickup 对象。
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>收集 Pickup 对象
以下步骤来自 [Unity 教程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141)。 我们将更新 Player，以使其能够通过与 Pickup 对象相撞来对其进行“收集”。 

1. 打开附加到 Player 对象的 **PlayerController** 脚本以进行编辑，并将其更新至以下格式：  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. 创建一个名为 **Pick Up** 的**标记**（它必须与脚本中的名称相匹配）。  
   
    ![][33]
   
    ![][34]
3. 将该**标记**应用到 Prefab Pickup 对象。 
   
    ![][35]
4. 为 Prefab 对象选中“IsTrigger”复选框。
   
    ![][36]
5. 将刚体添加到 Pickup Prefab 对象。 为了性能优化，我们将更新用于动态碰撞体的静态碰撞体。 
   
    ![][37]
6. 最后检查 Prefab 对象的“IsKinematic”属性。 
   
    ![][38]
7. 在 Unity 编辑器中单击“Play”即可启动**滚动球**游戏，该游戏使用键盘键作为方向输入来移动 Player 对象。 

### <a name="updating-the-game-for-mobile-play"></a>更新游戏以在移动设备上运行
前面的部分总结了来自 Unity 的基本教程。 现在，我们将对游戏进行修改，以使其能够更友好地在移动设备上运行。 请注意，目前我们对游戏使用的是键盘输入进行测试。 现在，我们将对其进行修改，以通过使用手机动作等来控制 Player。 将加速计用作输入。 

打开 **PlayerController** 脚本进行编辑，并更新 **FixedUpdate** 方法，以使用加速计的动作来移动 Player 对象。 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

本教程总结了使用 Unity 创建基本游戏的过程，可将其部署到想要玩游戏的设备上。 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













