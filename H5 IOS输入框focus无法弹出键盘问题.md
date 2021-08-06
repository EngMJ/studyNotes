## 原因
键盘需要用户触发才能弹出.导致autofocus或者element.focus()无效
## 解决方案:
* 在键盘弹出的情况下再去focus，或者跳转到带有autofocus的页面也就可以正常focus 
    * 1. 提供占位的input诱导点击,使输入框focus调起键盘,再focus目标input.
    * 2. 提供占位input,再接下来的逻辑中继续使用该input

