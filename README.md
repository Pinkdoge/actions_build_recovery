# 利用Github Actions编译TWRP

目前大部分功能都已经过测试，上传功能由于之前失败导致触发了API使用上限将等到2020/06/21测试

由于编译时间较长，建议把<code>.github/workflows/actions_twrp.yml</code>末尾上传处的<code>${{ secrets.GITHUB_TOKEN }}</code>改成自己的Personal Access Token

注意保护自己的Personal Access Token，将它放入仓库Settings里的Secret里后用<code>${{ secrets.YOUR_TOKEN_NAME }}</code>来替换<code>${{ secrets.GITHUB_TOKEN }}</code>

比如我的secret名字叫做work.则使用<code>${{ secrets.work }}</code>

## 配置

配置文件是config.json

<code>twrp_url</code>中填入TWRP仓库源地址

<code>twrp_branch</code>中填入TWRP源码分支

<code>git_username</code>中填入您的Git用户名

<code>git_email</code>中填入您的Git邮箱（Github可使用<code>Github ID+Github用户名@users.noreply.github.com</code>）

<code>use_own_dt</code>，bool值，选择<code>true</code>后以下三项起效

• <code>dt_url</code> 设备树地址

• <code>dt_branch</code>设备树分支

• <code>dt_path</code> 设备树将要Clone到的本地地址（相对路径）

<code>device_code</code>机型代号（如Honor 5X是kiwi）

### 修复问题

<code>fix_product</code> 修复不能找到设备的bug，bool值

<code>fix_misscom </code>修复缺少device/qcom/common的bug，bool值

<code>fix_busybox</code> 修复缺少busybox的bug，bool值

<code>fix_branch</code> 修复以上bug的分支

## 开始

对config.json进行修改会立即开始

同时每周进行一次自动编译

## 编译结果
可以在[Release](https://github.com/Insouciant21/action_build_twrp/releases)下载
