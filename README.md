# AutoBuildImmortalWrt
[![GitHub](https://img.shields.io/github/license/wukongdaily/AutoBuildImmortalWrt.svg?label=LICENSE&logo=github&logoColor=%20)](https://github.com/wukongdaily/AutoBuildImmortalWrt/blob/master/LICENSE)
![GitHub Stars](https://img.shields.io/github/stars/wukongdaily/AutoBuildImmortalWrt.svg?style=flat&logo=appveyor&label=Stars&logo=github)
![GitHub Forks](https://img.shields.io/github/forks/wukongdaily/AutoBuildImmortalWrt.svg?style=flat&logo=appveyor&label=Forks&logo=github) [![Github](https://img.shields.io/badge/RELEASE:AutoBuildImmortalWrt-123456?logo=github&logoColor=fff&labelColor=green&style=flat)](https://github.com/wukongdaily/AutoBuildImmortalWrt/releases) [![Bilibili](https://img.shields.io/badge/Bilibili-123456?logo=bilibili&logoColor=fff&labelColor=fb7299)](https://www.bilibili.com/video/BV1EG6VYCER3) [![操作步骤](https://img.shields.io/badge/YouTube-123456?logo=youtube&labelColor=ff0000)](https://youtu.be/xIVtUwZR6U0)

## 🤔 这是什么？
它是一个工作流。可快速构建 带docker且支持自定义固件大小的 immortalWrt
> 1、支持自定义固件大小 默认1GB <br>
> 2、支持预安装docker（可选）<br>
> 3、目前支持x86-64 和 rockchip 两个平台（后续会增加）<br>
> 4、新增支持MT3000/MT2500/MT6000/B2200（docker可选）<br>
> 5、新增全志平台R1S、香橙派Zero3等机型的工作流<br>
> 6、新增用户预设置pppoe拨号功能<br>
> 7、新增树莓派①②③④⑤<br>
> 8、新增第三方软件包的集成功能 https://github.com/wukongdaily/AutoBuildImmortalWrt/discussions/209

## 如需通过自己服务器构建
1. 克隆源码仓库并进入目录
```bash
git clone https://github.com/lklbjn/AutoBuildImmortalWrt.git
cd AutoBuildImmortalWrt
```
2. 执行 Docker 命令开始编译
```bash
docker run --rm -i \
  --user root \
  -v "./bin:/home/build/immortalwrt/bin" \
  -v "./files:/home/build/immortalwrt/files" \
  -v "./x86-64/imm.config:/home/build/immortalwrt/.config" \
  -v "./shell/prepare-packages.sh:/home/build/immortalwrt/prepare-packages.sh" \
  -v "./x86-64/24.10/build.sh:/home/build/immortalwrt/build.sh" \
  -e PROFILE=1024 \
  # -e INCLUDE_DOCKER=false \
  # -e ENABLE_PPPOE=true \
  # -e PPPOE_ACCOUNT=19315067759 \
  # -e PPPOE_PASSWORD=ciallo \
  immortalwrt/imagebuilder:x86-64-openwrt-24.10.2 /bin/bash /home/build/immortalwrt/build.sh
```
### 可选环境变量
* PROFILE<br/>
  设备可用存储空间（MB），示例中设为 1024。
* INCLUDE_DOCKER<br/>
  是否在固件中包含 Docker 支持，false 表示不包含。
  > 不需要的话，执行时直接删除那一行就行了
* ENABLE_PPPOE<br/>
  是否启用 PPPoE，true/false。
  > 不需要的话，执行时直接删除那一行就行了
* PPPOE_ACCOUNT / PPPOE_PASSWORD<br/>
  如果启用 PPPoE，可在此设置拨号账号和密码。
  > 不需要的话，执行时直接删除那一行就行了

## 如何查询imm仓库内有哪些插件
https://mirrors.sjtug.sjtu.edu.cn/immortalwrt/releases/24.10.2/packages/x86_64/luci/
## 如何查询imm仓库外目前可以集成哪些插件
https://github.com/wukongdaily/store/tree/master/run
> 具体方法 https://github.com/wukongdaily/AutoBuildImmortalWrt/discussions/209
## 第三方插件集成的原理详见以下视频
https://www.youtube.com/watch?v=7i6BQeitUtE

## 旁路由的用户必读
近期不少用户修改配置文件中的默认ip地址，误认为这个工作流可以直接设置旁路ip。这是巨大的误解，这样设置就乱套了。<br>
旁路的逻辑应该是单网口模式。根据下面的固件属性可知。单网口默认采取`dhcp模式`，用户应当自行在上一级路由器查看给imm路由器分配的ip地址。
然后通过该ip来访问imm后台页面，在imm后台页面中，根据自己主路由的网段 自行配置旁路的ip地址。

## 正常路由模式必读
所谓正常的路由模式 就是指多网口用户，多网口的意思就是2个或者2个以上网口的情况。<br>
一般wan用于拨号或者自动获取ip <br>
而其他lan一般是给其他设备分配dhcp<br>
这种情况下 你可以修改路由器的默认ip  `192.168.100.1` 比如你可以修改为`192.168.80.1 ` 诸如此类。<br>
没错，修改此ip 无非就是为了避免跟光猫或者跟家庭中的其他路由器网段冲突。大多数用户，无需更改。

## 该固件默认属性？(必读)
- 该固件刷入【单网口设备】默认采用DHCP模式,自动获得ip。类似NAS的做法
- 该固件刷入【多网口设备】默认WAN口采用DHCP模式，LAN 口ip为  `192.168.100.1` <br>其中eth0为WAN 其余网口均为LAN
- 若用户在工作流中勾选了拨号信息 则WAN口模式为pppoe拨号模式。
- 建议拨号用户使用之前重启一次光猫。
- 综合上述特点，【单网口设备】应该先接路由器，先在上级路由器查看一下它的ip 再访问。
- 上述特点 你都可以通过 `99-custom.sh` 配置和调整

## ❤️其它GitHub Action项目推荐🌟 （建议收藏）⬇️
- ### [一键生成run插件] 🆕
- https://github.com/wukongdaily/RunFilesBuilder<br>
- ### [一键生成docker离线镜像] 🆕
- https://github.com/wukongdaily/DockerTarBuilder<br>
- ### [OpenWrt/Armbian IMG安装器ISO] 🆕
- https://github.com/wukongdaily/armbian-installer


## ❤️如何构建docker版ImmortalWrt（建议收藏）⬇️
https://wkdaily.cpolar.cn/15
# 🌟鸣谢
### https://github.com/immortalwrt
### https://github.com/sirpdboy
### https://github.com/wukongdaily/ib-overlay

## ❤️赞助作者 ⬇️⬇️

[![点击这里赞助我](https://img.shields.io/badge/点击这里赞助我-支持作者的项目-orange?logo=github)](https://wkdaily.cpolar.cn/01)




<details>
<summary><h2>🍭相关引用</h2></summary>

#### 🍭引用和项目参考的仓库
- https://github.com/wukongdaily/RunFilesBuilder
- https://github.com/wukongdaily/store
- https://github.com/xiaorouji/openwrt-passwall
- https://github.com/xiaorouji/openwrt-passwall2
- https://github.com/sirpdboy/luci-theme-kucat
- https://github.com/AdguardTeam/AdGuardHome
- https://github.com/kiddin9/kwrt-packages
