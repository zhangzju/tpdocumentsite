## 基本信息

### Maintainer

<strong>[彭炯 SW2](https://spcodes.rd.tp-link.net/pengjiong)</strong>

### CI 地址

<http://spjenkins.rd.tp-link.net:8080/job/RE200(SP)v2_buildimage>

### Document support

<strong>[张伟　SW1](https://spcodes.rd.tp-link.net/zhangwei_w8284)</strong>

## RE200(SP)v2代码编译步骤

1. 拉取代码。
```shell
git clone https://spcodes.rd.tp-link.net/sw2/linux_mtk_RE200_SP_v2.git
```
2. 建立好platform和SDK的映射。进入__platform_BBA1.5/build__目录，执行命令
```
make MODEL=RE200SPV2 sdk_config
```
menuconfig唯一的选项里面，填入SDK的目录名: MTK_APSoC_SDK_5020，保存退出。
3. 代码编译。
```shell
make MODEL=RE200SPV2 env_build
make MODEL=RE200SPV2 tools_build
make MODEL=RE200SPV2 all
```

## TP boot烧录
1. 开机阶段，按数字'4'进入boot命令行
2. 配置boot网络。
```
setenv serverip 192.168.1.100  /* 这个是PC的IP */
setenv ipaddr 192.168.1.254    /* 这个是DUT的IP */
saveenv                        /* 保存参数的配置 */
```
3. 上传boot并擦写boot,boot位于 __SDK/tplink/output/RE200SPV2/boot/boot.bin__。
```
tftp 82000000 boot.bin  /* 注意地址82000000一定不能错，上传boot */
erase uboot
cp.uboot
```

## TP image烧录
1. 开机阶段，按数字'4'进入boot命令行
2. 配置boot网络。
```
setenv serverip 192.168.1.100  /* 这个是PC的IP */
setenv ipaddr 192.168.1.254    /* 这个是DUT的IP */
saveenv                        /* 保存参数的配置 */
```
3. 上传image并擦写image。升级文件位于__SDK/tplink/output/RE200SPV2/image/RE200_UP.bin__
```
tftp 82000000 RE200_UP.bin  /* 注意地址82000000一定不能错，上传image */
erase  linux
cp.linux
```