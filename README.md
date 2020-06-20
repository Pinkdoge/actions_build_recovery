# 利用github actions编译twrp

## 配置

配置文件在[config.json](https://github.com/Insouciant21/actions_build_twrp/blob/master/config.json)中

<code>TWRP_URL</code>中填入TWRP仓库源地址

<code>TWRP_BRANCH</code>中填入TWRP源码分支

<code>GIT_USERNAME</code>中填入您的Github用户名

<code>USE_OWN_DT</code>，bool值，选择<code>true</code>后以下三项起效

​	<code>DT_URL</code> 设备树地址

​	<code>DT_BRANCH</code>设备树分支

​	<code>DT_PATH</code> 设备树将要Clone到的本地地址（相对路径）

<code>DEVICECODE</code>机型代号（如Honor 5X是kiwi）

### 修复问题

<code>FIX_PRODUCT</code> 修复不能找到设备的bug，bool值

<code>FIX_MISSCOM </code>修复缺少device/qcom/common的bug，bool值

<code>FIX_BUSYBOX</code> 修复缺少busybox的bug，bool值

<code>FIX_BRANCH</code> 修复以上bug的分支

## 开始

先fork这个仓库，然后点击右上角Star

## 编译结果

可以在[Release](https://github.com/Insouciant21/action_build_twrp/releases)下载或是在[Actions Log](https://github.com/Insouciant21/actions_build_twrp/actions)中寻找链接下载