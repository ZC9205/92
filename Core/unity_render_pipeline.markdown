- [Render Pipeline（渲染管线）](#render-pipeline渲染管线)
  - [种类](#种类)
  - [SRP(Scriptable Render Pipeline 可编程渲染管线)](#srpscriptable-render-pipeline-可编程渲染管线)
  - [Dynamic Range（动态范围）](#dynamic-range动态范围)
    - [SDR（Standard Dynamic Range 低动态范围）](#sdrstandard-dynamic-range-低动态范围)
    - [HDR（High Dynamic Range 高动态范围）](#hdrhigh-dynamic-range-高动态范围)
      - [HDR Rendering](#hdr-rendering)
      - [HDR Output](#hdr-output)
      - [Tonemapping](#tonemapping)

# Render Pipeline（渲染管线）
## 种类
![Alt text](assets/unity_render_pipeline/image.png)




## SRP(Scriptable Render Pipeline 可编程渲染管线)




## Dynamic Range（动态范围）
亮度显示的范围
### SDR（Standard Dynamic Range 低动态范围）
RGB单个通道值为8bit大小，也就是0~255
### HDR（High Dynamic Range 高动态范围）
RGB单个通道值范围大于8bit，存储类型为float

HDR通过更广阔的取值范围可以更好的反映现实世界的颜色，更好地显示效果
在管线或相机中设置HDR
![Alt text](assets/unity_render_pipeline/image-1.png)
![Alt text](assets/unity_render_pipeline/image-2.png)
#### HDR Rendering
将场景（Scene）内容渲染到HDR图，并执行渲染操作（例如后处理效果）
#### HDR Output
将渲染后的HDR目标内容呈现在显示设备上（需要硬件支持）
#### Tonemapping
当设备只支持显示SDR时，将HDR内容转换成SDR，以便在设备上显示