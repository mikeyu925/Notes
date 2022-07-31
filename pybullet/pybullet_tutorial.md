# Pybullet使用教程

安装完成之后主要有5个文件夹需要了解：
-  pybullet_data：中包含了一系列的实物模型
-  pybullet_envs：包含现成的环境可以用来进行操作
-  pybullet_examples: 包含一些exmpales用来学习如何构建任务，以及使用强化学习来进行 仿真 和 训练
-  pybullet_robots: 内部包含有2种机械臂（xarm，pandas）和1种机械狗（laikago）
-  pybullet_utils：一些工具函数


## 第一部分

### connect 方法连接物理引擎
pybullet的结构可以理解为客户端和服务端，客户端发送指令，服务端来执行。
```python
# 连接物理引擎
physicsCilent = p.connect(p.GUI)
```
connect函数接受一个参数，代表用户选择连接的物理引擎服务器（physics server），可选的有pybullet.GUI和pybullet.DIRECT ，返回一个数字代表服务器的ID。
- pybullet.GUI: 实施渲染场景到GUI上
- pybullet.DIRECT: 不允许调用内置的渲染器件、外部的openGL

> 当然，除了GUI和DIRECT模式，pybullet还支持使用SHARED_MEMORY、UDP或TCP网络连接到同一台机器上的不同进程或远程机器上的物理服务器。

同样，使用完毕后我们需要断开连接：
```python
    # 断开连接
    p.disconnect()
```
使用disconnect()函数可以指定需要关闭的物理服务器，默认参数是0，也就是第一个创建的服务器的ID。

### 渲染配置configureDebugVisualizer

一些常见的渲染配置：
```python
    p.configureDebugVisualizer(p.COV_ENABLE_RENDERING, 0) #  禁止渲染 不希望引擎去渲染没有加载好的场景
    p.configureDebugVisualizer(p.COV_ENABLE_RENDERING, 1) # 打开渲染
    p.configureDebugVisualizer(p.COV_ENABLE_GUI, 0) # 取消渲染时候周围的控制面板
    p.configureDebugVisualizer(p.COV_ENABLE_TINY_RENDERER, 0) # 禁用tinyrenderer，不让CPU上的集成显卡来参与渲染工作。
    p.configureDebugVisualizer(p.COV_ENABLE_SINGLE_STEP_RENDERING)  # 一步步地渲染
```
onfigureDebugVisualizer接受两个参数，一个是渲染配置的选项，第二个是该选项的取值


### 设置环境重力加速度  setGravity

```python
# 设置环境重力加速度
p.setGravity(0, 0, -10)  # 
```
三个参数分别是x,y,z三个方向上的重力加速度值

### 加载模型  loadURDF
pybullet提供了非常方便的函数loadURDF来加载外部的urdf文件，返回值是创建的模型对象的ID，如果无法加载URDF文件，则此整数将为负数，而不是有效的id。接受的参数有8个，其中第一个是必填参数。
```python
loadURDF(fileName, basePosition=None, baseOrientation=None, useMaximalCoordinates=0, useFixedBase=0, flags=0, globalScaling=1.0, physicsClientId=0)
```

- fileName:代表需要加载的urdf模型的绝对路径。

前面提到过pybullet_data中存放了许多现成的urdf文件。因此，对于存放在pybullet_data文件夹目录下的urdf文件，不仅可以输入r2d2.urdf的绝对路径，也可以先通过pybullet_data.getDataPath() 获取pybullet_data的绝对路径，然后将该路径添加进入模型的搜索路径中：
```python
# 添加资源路径
p.setAdditionalSearchPath(pybullet_data.getDataPath())
```
这样，凡是在pybullet_data这个文件夹下的模型，我们都可以直接使用它们的文件名加载，而不需要再输入绝对路径了。
```python
    planeId = p.loadURDF("plane.urdf") # 加载urdf模型

    # 加载机器人，并设置加载的机器人的位姿
    startPos = [0, 0, 1]
    startOrientation = p.getQuaternionFromEuler([0, 0, 0]) # 从 欧拉角 转换得到 四元数
    boxId = p.loadURDF("r2d2.urdf", startPos, startOrientation)
```
- useMaximalCoordinates：设置为True的话会导致无法读取模型的连杆名称，并且无法获取到碰撞事件。
- useFixedBase：模型加载进来后，使base连杆保持固定

常见urdf文件：
```python
p.setAdditionalSearchPath(pd.getDataPath())  # 设置pybullet_data的文件路径
p.loadURDF("husky/husky.urdf", basePosition=[3, 0, 0])  # 车
p.loadURDF("racecar/racecar.urdf", basePosition=[-3, 0, 0], globalScaling=5)  # 车
p.loadURDF("differential/diff_ring.urdf", basePosition=[5, 0, 0])  # 齿轮
p.loadURDF("lego/lego.urdf", basePosition=[-3, 0, 0], globalScaling=20)  # 箱子
p.loadSDF("stadium.sdf")  # 草坪
p.loadURDF("objects/mug.urdf", basePosition=[-3, 0, 0], globalScaling=5)  # 水杯
trayUid=p.loadURDF("tray/traybox.urdf", basePosition=[0, 0, 0.65])  # 盒子
objectUid=p.loadURDF("random_urdfs/000/000.urdf", basePosition=[0, 0, 0.7])  # 小木块
tableUid=p.loadURDF("table/table.urdf", basePosition=[0, 0, 0])  # 桌子
tableUid=p.loadURDF("table_square/table_square.urdf", basePosition=[-5, 0, 0])  # 桌子
p.loadURDF("plane100.urdf", useMaximalCoordinates=True)  # 地面
floor_id = p.loadURDF("plane.urdf", useMaximalCoordinates=False)  # 地面
pandaUid=p.loadURDF("franka_panda/panda.urdf", basePosition=[-0.5, 0, 0.65], useFixedBase=True)  # 机械臂
robot = p.loadURDF("mini_cheetah/mini_cheetah.urdf", basePosition=[2, 0, 1])  # 四足机器人
p.loadURDF("quadruped/spirit40.urdf", basePosition=[-2, 0, 0], globalScaling=5)  # 四足机器人
```

### 步进模拟 stepSimulation

```python
for i in range(1000):
    p.stepSimulation()
    time.sleep(1. / 240.)
```
即利用正向动力学进行步进模拟。由于计算中模拟物理过程还是离散得模拟的，因此，使用stepSimulation可以看成是进行一次迭代步。也可以看成是RL环境中各个模型对象在一个极短的时间内交互一次。


### 获得机器人关节信息

```python
 getNumJoints(robotID)  # 函数返回该ID机器人的关节数量。

 getJointInfo(robotID,JointID) # 函数返回该ID机器人的关节序号为JointID的关节信息。
 ```
 
 其中关节类型为4的关节不可使用，即:p.JOINT_FIXED

第一部分整体代码：
```python
import pybullet as p
import time
import pybullet_data
if __name__ == '__main__':
    p.connect(p.GUI)  # 对物理引擎进行链接
    p.setGravity(0,0,-10) # 设置中坜加速度

    p.configureDebugVisualizer(p.COV_ENABLE_RENDERING, 0)
    p.configureDebugVisualizer(p.COV_ENABLE_GUI, 0) # 取消渲染时候周围的控制面板

    p.setAdditionalSearchPath(pybullet_data.getDataPath()) # 使用相对路径读入库中现成的模型
    planeId = p.loadURDF("plane.urdf") # 加载urdf模型

    # 加载机器人，并设置加载的机器人的位姿
    startPos = [0, 0, 1]
    startOrientation = p.getQuaternionFromEuler([0, 0, 0]) # 从 欧拉角 转换得到 四元数
    boxId = p.loadURDF("r2d2.urdf", startPos, startOrientation)

    # 按照位置和朝向重置机器人的位姿，由于我们之前已经初始化了机器人，所以此处加不加这句话没什么影响
    p.resetBasePositionAndOrientation(boxId, startPos, startOrientation)
    p.configureDebugVisualizer(p.COV_ENABLE_RENDERING, 1)

    p.setTimeStep(1. / 240.)
    p.setRealTimeSimulation(1)

    while True:
        pass

    # for i in range(10000):
    #     p.stepSimulation()
    #     time.sleep(0.1)
    #     # time.sleep(1. / 240.)
    # # 获取机器人位置 及 方向四元数
    # cubePos, cubeOrn = p.getBasePositionAndOrientation(boxId)
    # print("pos:" + str(cubePos), "orientation:" + str(cubeOrn))

    # 断开连接
    p.disconnect()
```

## 第二部分

### 机器人操控

关节电机控制相关API:
```python
setJointMotorControl2(robotID,jointIndex,controlMode) #  控制某个机器人的某个关节电机

p.setJointMotorControlArray( # 一次性设置多个电机参数
        bodyUniqueId, # 机器人id
        jointIndices, # 关节电机索引 list
        controlMode, # 控制模式
        targetVelocities, # 目标速度
        forces # 加速度
    )
```

### 获取及重置base的位置和姿态 
```python
# 获取位置及姿态
getBasePositionAndOrientation(objectUniqueId，physicsClientId)
```
获取机器人base连杆在世界坐标系中的位置和姿态四元数，返回3个浮点数表示的位置列表和4个浮点数表示的姿态列表[x、y、z、w]。

```python
# 设置位置及姿态
resetBasePositionAndOrientation(bodyUniqueId, posObj, ornObj, physicsClientId)
```
重置每个对象的base连杆的位置和姿态。最好只在开始时这样做，而不是在运行模拟期间，因为该命令将覆盖所有物理模拟的效果。
```python
# 简单的示例
startPos = [0, 0, 1]
startOrientation = p.getQuaternionFromEuler([0, 0, 0])
robot_id = p.loadURDF("BigCat/urdf/BigCat.urdf", basePosition = [0, 2, 1], useMaximalCoordinates=True)
p.resetBasePositionAndOrientation(robot_id, startPos, startOrientation)  # 没有返回参数
```


### 获取及重置base的速度

获取物体base连杆的线速度和角速度：
```python
getBaseVelocity(bodyUniqueId, physicsClientId)
```
返回两个向量列表，表示笛卡尔世界空间坐标中的线速度[x, y, z]和角速度[wx, wy, wz]。

重置base连杆的线速度和角速度：
```python
resetBaseVelocity(bodyUniqueId, linearVelocity, angularVelocity, physicsClientId)
```


### 相机追踪
在真实或者展示时，我们希望想demo里面一样，我们的机器人一直在画面中心，这个时候，就需要调用resetDebugVisualizerCamera函数来设置当前相机的位置和朝向，那么我只需要在每次迭代步结束后，调用这个函数来调整相机位置就行。
```python
    location ,_ = p.getBasePositionAndOrientation(robot_id)
    p.resetDebugVisualizerCamera(
        cameraDistance=3,
        cameraYaw=110,
        cameraPitch=-20,
        cameraTargetPosition=location
    )
```

### 状态记录

搭建模拟环境测试算法时，有时在调试过程中我们会需要能够知道模拟的过程中发生了什么，毕竟我们不可能每时每刻都在电脑旁，而且训练时一般是不会开渲染的。pybullet中提供了几个保存当前环境状态的函数，saveState, saveBullet, restoreState。
-  saveState : 将目前模拟器的状态保存到内存中，让这段程序后面可以随时读取内存中的这个模拟器状态，然后载入这个存档，因此需要指定模拟器环境ID，返回一个状态ID
-  saveBullet : 将状态保存到磁盘上，需要接受模拟器ID和路径两个参数。
-  restoreState : 读取由  saveState 和 saveBullet 保存的状态
    >注意的是使用restoreState读取后，整个模拟器会变成载入状态，包括各个模型对象的各分支的动力学参数，所以在使用restoreState之前，需要确保需要载入的模型都已经加载进来了。

除此之外，如果你还想得到模拟器渲染的视频，可以使用startStateLogging和stopStateLogging两个函数完成，前者表示开始记录，后者表示记录结束，后者以前者的返回值作为参数。如下所是：
```python
log_id = p.startStateLogging(p.STATE_LOGGING_VIDEO_MP4, "log/robotmove.mp4")

"""
能够渲染的代码片段
"""
p.stopStateLogging(log_id)
```
> 注意：需要确保你的电脑上有ffmpeg，并且其bin目录已经添入环境变量中，因为pybullet就是通过ffmpeg将视频流写入mp4文件的。


### 碰撞检测

https://blog.csdn.net/weixin_41045354/article/details/104797926


首先我们需要创建一个基本的模型——墙，主要用到如下三个方法。
- createVisualShape负责创建视觉模型
- createCollisionShape负责创建碰撞箱模型
- createMultiBody则是负责将视觉模型和碰撞箱模型整合在一起形成一个完整的物理模型对象，并可以加入一些额外的参数，比如质量，转动惯量。

```python
# 创建一面墙的视觉模型
visual_shape_id = p.createVisualShape(
    shapeType=p.GEOM_BOX,
    halfExtents=[60, 5, 5]
)
```
> shapeType指定了创建的模型的类型，可选的有GEOM_SPHERE, GEOM_BOX, GEOM_CAPSULE, GEOM_CYLINDER, GEOM_PLANE, GEOM_MESH。

```python
# 创建一面墙的碰撞模型
collison_box_id = p.createCollisionShape(
    shapeType=p.GEOM_BOX,
    halfExtents=[60, 5, 5]
)
```
创建完视觉模型和碰撞箱模型后，我们需要将两者使用createMultiBody结合起来：
```python
# 使用创建的视觉形状和碰撞箱形状使用createMultiBody将两者结合在一起
wall_id = p.createMultiBody(
    baseMass=10000,
    baseCollisionShapeIndex=collison_box_id,
    baseVisualShapeIndex=visual_shape_id,
    basePosition=[0, 10, 5]
)
```

### pybullet 中画点和线的api

addUserDebugLine:

|  是否必要 | 参数名 |  参数类型 | 解释 |
|  ----  | ----  | ---- | ---- |
| 需要  | lineFromXYZ | vec3 | 轨迹开始位置 |
| 需要  | lineToXYZ | vec3 | 轨迹目标位置 |
| 可选  | lineColorRGB | vec3 | RGB颜色（0-1) |
| 可选  | lineWidth | float | 线宽 |
| 可选  | lifeTime | float | 线条持续时间 |


addUserDebugPoints

## 第三部分

### 自定义仿真界面

```python
    # 在仿真界面自定义参数滑块，分别为速度，驱动力、转向
    targetVelocitySlider = p.addUserDebugParameter("wheelVelocity", -10, 10, 0)  # 参数名称 最小值 最大值 起始值
    maxForceSlider = p.addUserDebugParameter("maxForce", 0, 10, 10)
    steeringSlider = p.addUserDebugParameter("steering", -0.5, 0.5, 0)


    # 读取仿真界面的参数的值，读取速度，转向角度，驱动力参数
targetVelocity = p.readUserDebugParameter(targetVelocitySlider)
maxForce = p.readUserDebugParameter(maxForceSlider)
steeringAngle = p.readUserDebugParameter(steeringSlider)
```

### urdf文件概述

父子关系树：连杆link1 --> 关节joint1 --> 连杆link2 --> 关节joint2 ······

每根连杆包括：

- 可视化模型 visual：包括初始姿态rpy、原点位置xyz，几何形状geometry
- 碰撞检测模型 collision：包括初始姿态rpy、原点位置xyz，几何形状geometry
- 惯性计算模型 inertial：包括初始姿态rpy、原点位置xyz，质量mass、惯性张量

```html
  <link name="FR_thigh">

    <visual>
      <origin rpy="0 0 0" xyz="0 0 0"/>
      <geometry>
        <mesh filename="../meshes/thigh_mirror.stl" scale="1 1 1"/>
      </geometry>
      <material name="orange"/>
    </visual>

    <collision>
      <origin rpy="0 1.57079632679 0" xyz="0 0 -0.1"/>
      <geometry>
        <box size="0.2 0.0245 0.034"/>
      </geometry>
    </collision>

    <inertial>
      <origin rpy="0 0 0" xyz="-0.003237 0.022327 -0.027326"/>
      <mass value="1.013"/>
      <inertia ixx="0.005529065" ixy="-4.825e-06" ixz="0.000343869" iyy="0.005139339" iyz="-2.2448e-05" izz="0.001367788"/>
    </inertial>

  </link>
  ```

  每个关节包括：初始姿态、坐标原点、父连杆、子连杆

  ```html
  <joint name="floating_base" type="fixed">
    <origin rpy="0 0 0" xyz="0 0 0"/>
    <parent link="base"/>
    <child link="trunk"/>
  </joint>
  ```


  ## 任务部署

-  了解模型  ok
-  能够使得动起来  ok 
-  轨迹描绘
-  环境编写
-  强化学习算法编写
-  训练
  


  ## 问题报错

  ### Cannot load URDF file.

  一般是路径问题，将路径改为绝对路径。