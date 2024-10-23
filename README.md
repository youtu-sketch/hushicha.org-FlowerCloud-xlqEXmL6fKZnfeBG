
ijkplayer是一款由B站研发的移动端国产播放器，它基于FFmpeg3\.4版本，同时兼容Android和iOS两大移动操作系统。ijkplayer的源码托管地址为https://github.com/bilibili/ijkplayer，截止2024年9月15日，ijkplayer获得3\.24万星标数，以及0\.81万个分支数，而这还是ijkplayer停止更新6年之后的数据，可想而知当年的ijkplayer是多么火爆。
 不过正因为ijkplayer多年未更新，按照导包方式仅能在较老的平台上编译运行，比如ijkplayer支持的Android平台仅限于API 9～23，支持的iOS平台仅限于iOS 7\.0～10\.2\.x。为了让ijkplayer能够在更新的开发环境上正常运行，需要先在Linux系统上交叉编译ijkplayer在Android平台上的so文件，才能在App工程中导入并调用so库。下面介绍如何在Linux编译ijkplayer的so库。


# 一、准备Linux编译环境


首先在Linux系统执行下面命令安装编译工具。




```
yum install git make yasm
```


接着执行下面命令临时调整tmp分区大小，确保系统的临时空间充足，避免解压大文件失败。




```
mount -o remount,size=2G /tmp
```


# 二、安装Android的SDK和NDK


依次执行下列命令下载并安装Android的SDK，注意不要用太高版本的SDK，因为ijkplayer没有适配高版本的SDK。




```
mkdir -p /usr/local/src_ijkplayer
cd /usr/local/src_ijkplayer
curl -O https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
unzip sdk-tools-linux-4333796.zip
mkdir sdk
mv tools sdk/cmd_tools
cd sdk/cmd_tools/bin
./sdkmanager "build-tools;28.0.3" "platforms;android-28"
```


依次执行下列命令下载并安装Android的NDK，注意不要用太高版本的NDK，因为ijkplayer没有适配高版本的NDK，官方推荐采用r10e版本的NDK即可。




```
cd /usr/local/src_ijkplayer
curl -O https://dl.google.com/android/repository/android-ndk-r10e-linux-x86_64.zip
unzip android-ndk-r10e-linux-x86_64.zip
```


执行下面的环境变量设置命令，分别设置SDK的环境变量ANDROID\_SDK，以及NDK的环境变量ANDROID\_NDK。




```
export ANDROID_SDK=/usr/local/src_ijkplayer/sdk
export ANDROID_NDK=/usr/local/src_ijkplayer/android-ndk-r10e
```


# 三、下载并编译ijkplayer


先执行以下命令下载ijkplayer的源码包。




```
cd /usr/local/src_ijkplayer
git clone https://github.com/Bilibili/ijkplayer.git
```


再执行以下命令检查并初始化ijkplayer的Android编译环境。




```
cd ijkplayer
./init-android-openssl.sh
./init-android.sh
```


然后依次执行下列命令，分别编译ijkplayer需要的openssl库和ffmpeg库，以及ijkplayer的so库。之所以在三个脚本后面添加“ arm64”，是为了只编译适配arm64指令的so文件。




```
cd android/contrib
./compile-openssl.sh arm64
./compile-ffmpeg.sh arm64
cd ../
./compile-ijk.sh arm64
```


一切顺利的话，即可在ijkplayer/android/ijkplayer/ijkplayer\-arm64/src/main/libs/arm64\-v8a目录下看到编译好的三个so库：libijkffmpeg.so、libijkplayer.so、libijksdl.so。把包含三个so文件在内的整个libs目录复制到App工程的libs目录，即可完整ijkplayer的so库导入工作。


更多详细的FFmpeg开发知识参见[《FFmpeg开发实战：从零基础到短视频上线》](https://item.jd.com/14020415.html "《FFmpeg开发实战：从零基础到短视频上线》")一书。


 本博客参考[wgetCloud机场](https://tabijibiyori.org)。转载请注明出处！
