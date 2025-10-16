# 小白也能学会的 rime + 万象拼音 输入法安装教程
## 第一步: 下载安装rime输入法
rime官网: [rime官网](https://rime.im/)

mac里叫`鼠须管` windows里叫`小狼毫`

mac需要在 `设置>键盘>文字输入>编辑` 里添加`鼠须管`输入法

安装完成后先别着急使用

## 第二步: 下载万象拼音
国内访问不了GitHub的可以访问[万象拼音gitee仓库](https://gitee.com/tong_he_jiao/rime_wan_xiang)

注: gitee上的东西不是万象官方地址,只是我为了方便创建的仓库.如果你有能力可以支持一下万象的作者

打开网页后,有个`克隆/下载` 的按钮,点一下,然后在弹窗里有一个`下载ZIP`的按钮,再点一下.

下载完成解压.会得到一个叫`rime_wan_xiang-main`的文件夹

还要下载一个语法模型(可以让你输入长句子更通顺),下载地址: [语法模型](https://gitee.com/tong_he_jiao/rime_wan_xiang/releases/tag/LTS)

把两个文件名为`Wanxiang-LTS-zh-hans` 的压缩包下载下来.解压后两个会合并成一个.gram文件

把这个.gram文件放到`rime_wan_xiang-main`的文件夹里

## 第三步: 替换rime的用户文件夹

打开rime用户文件夹:
- macos: /Users/~/Library/Rime/
- windows: C:\Users\\%userprofile%\AppData\Roaming\Rime

删除里面所有内容

把`rime_wan_xiang-main`的文件夹里的东西复制进去

点击rime输入法的`重新部署`按钮:
- macos: 切换到`鼠须管`输入法以后,点击菜单栏上输入法的按钮.就能看到`重新部署`按钮
- windows: 切换到`小狼毫`输入法以后,右键点击任务栏上的`中`或`英`的图标,就能看到`重新部署`按钮

等一会儿就能用了