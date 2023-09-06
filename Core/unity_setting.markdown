# Unity Setting（设置）


## Time
![Alt text](assets/unity_setting/image.png)

### Fixed Timestep 
固定帧FixedUpdate间隔及物理计算的时间长度
eg.Fixed Timestep设置为0.02=20毫秒 1秒50次
当上一帧到当前帧的时间为20毫秒，当前帧FixedUpdate会执行1次
当上一帧到当前帧的时间为100毫秒，当前帧FixedUpdate会执行5次

### Maximum Allowed Timestep
每帧固定帧FixedUpdate执行总时间及物理计算总时间不能超过该值
eg.Fixed Timestep设置为0.02=20毫秒，Maximum Allowed Timestep设置为0.333333=333毫秒
每帧FixedUpdate最大执行次数限制为333/20=16-17次
这么做目的是防止FixedUpdate执行时间过多导致产生无限卡顿（比如每个FixedUpdate花费20毫秒，这帧的执行次数过多会导致到下一帧的时间变长，从而导致下一帧产生更多的FixedUpdate次数，之后一帧比一帧多导致最后卡死）

## Quality

### VSync Count
![Alt text](assets/unity_setting/image-1.png)
垂直同步设置对<font color=#FF0000>iOS平台无效</font>
iOS平台可以使用<font color=#FF0000>Application.targetFrameRate</font>来设置帧率

#### Don't Sync
不设置垂直同步，实际帧率通过Application.targetFrameRate来指定

#### Every V Blank
帧率为60，Application.targetFrameRate无效

#### Every Second V Blank
帧率为30，Application.targetFrameRate无效

#### QualitySettings.vSyncCount
可以在代码中使用QualitySettings.vSyncCount来进行设置

