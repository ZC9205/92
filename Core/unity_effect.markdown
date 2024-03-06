- [Unity Effect（动效）](#unity-effect动效)
  - [Particle System（粒子效果）](#particle-system粒子效果)
    - [Main（主面板信息）](#main主面板信息)
      - [Duration](#duration)
      - [Looping](#looping)
      - [Start Delay](#start-delay)
      - [Start Lifetime](#start-lifetime)
    - [Emission（粒子发射）](#emission粒子发射)
      - [Rate over Time](#rate-over-time)
      - [Rate over Distance](#rate-over-distance)
      - [Bursts（一次性粒子发射）](#bursts一次性粒子发射)
        - [Time](#time)
        - [Count](#count)
        - [Cycles](#cycles)
        - [Interval](#interval)
        - [Probability](#probability)
        - [Example（举例）](#example举例)


# Unity Effect（动效）

## Particle System（粒子效果）

### Main（主面板信息）
#### Duration
粒子效果总持续时间，超过这个时间则整个粒子效果结束
如果勾选了Looping，则为一次循环的时间
#### Looping
是否持续循环效果。勾选后效果不会结束，会以一个Duration时间不断循环播放
#### Start Delay
开始延迟，延迟x秒后开始发射粒子
#### Start Lifetime
粒子被发射后存活的时间，超过时间后粒子消失

### Emission（粒子发射）
#### Rate over Time
每秒发射的粒子数量（粒子间有个短间隔，不会一次性一起发射）
#### Rate over Distance
发射粒子间的间隔距离
#### Bursts（一次性粒子发射）
##### Time
开始时间
##### Count
发射粒子数量（一次性）
##### Cycles
发射粒子总次数（如果在Duration时间内还没发射完会强制结束）
##### Interval
发射粒子次数的每秒间隔（该值最好不要小于等于粒子存活时间（Start Lifetime），否则粒子可能不能按预定间隔发射）
##### Probability
每次发射粒子的概率，1为这次必定发射，0为比不发射
##### Example（举例）
Duration = 5 
Start Lifetime = 1
Bursts.Time = 0
Bursts.Cycles = 3
Bursts.Interval = 2
粒子分别会在0、2、4秒各发射1次，总计3次
