1. adb 使用usb连接手机
	- `adb usb`



#### 一. scrcpy
1. 以 H.265（质量更好）捕获屏幕，将尺寸限制为 1920，将帧速率限制为 60fps，禁用音频，并通过模拟物理键盘来控制设备：
```
scrcpy --video-codec=h265 --max-size=1920 --max-fps=60 --no-audio --keyboard=uhid
scrcpy --video-codec=h265 -m1920 --max-fps=60 --no-audio -K  # short version
```