---
title: Jenkins自动化集成
date: 2016-05-24 18:09:47
tags: Jenkins
categories: 自动化
---

说明
---
Jenkins很强大，本文主要记录Jenkins用于iOS自动打包。
<!-- more -->
步骤
---
#### [Jenkins官网](https://jenkins.io)下载最新安装包

>如果下载太慢，可以使用我的备份[Jenkins1.651.2](https://coding.net/u/zzyhappyzzy/p/resourceRepo/git/raw/develop/packages/jenkins-1.651.2.pkg)

#### 双击下载的pkg文件，安装Jenkins。
#### 安装完成后，在浏览器输入` http://localhost:8080`可以看到Jenkins的页面  
![](/Jenkins/jenkins_1.png)  
#### 提升jenkins的用户权限为管理员

```
sudo dseditgroup -o edit -a jenkins -t user admin
sudo dscl . append /Groups/_developer GroupMembership jenkins (可选)
```

#### 补全Jenkins用户的信息

>打开系统偏好设置，选择用户与群组,输入密码解锁。选择没有名字的那个用户，重新设置下密码，然后右键->高级选项，补全名字

![](/Jenkins/jenkins_2.png)
![](/Jenkins/jenkins_3.png)

#### 从当前用户切换到jenkins。使用命令`su -l 用户名`(也可以重启电脑，选择jenkins用户登录，然后安装相关的证书。下面讲述的是命令行操作)
```
su -l jenkins
```

#### 为了方便从git等仓库拉取代码，生成SSH。(该过程最好全部默认，即一直按回车即可)
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" //邮箱为对应git仓库的用户名
```

#### 使用cat命令查看SSH并复制出来，然后在相应的git仓库配置SSH即可。

>由于我是命令行切换的用户，直接pbcopy是无用的，只能打印出来手动复制了。如果是登录时选择的Jenkins，可以使用`pbcopy > ~/.ssh/id_rsa.pub`复制SSH

```
cat ~/.ssh/id_rsa.pub
```

#### 创建证书文件夹MobileDevice。将证书*.mobileprovision复制到Provisioning Profiles文件夹下
```
cd /Users/Shared/Jenkins/Library
mkdir MobileDevice
cd MobileDevice
mkdir 'Provisioning Profile'
```

#### 配置Jenkins
* 安全配置
![](/Jenkins/jenkins_4.png)

* 插件配置(默认没有git插件，需要自己安装)
![](/Jenkins/jenkins_5.png)

#### XCode的工程配置里，`Code Signing`需要配置好相应的证书

#### 给个参考的shell配置（注：工程采用了cocoapods管理第三方库）

>重点在xcodebuild、xcrun命令

```shell
#xxx为具体工程名，记得替换掉，目录结构也可能不一致，需微调
echo "start execute shell"
echo $(pwd)
CURRENT_DIR=$(pwd)
GIT_VER_NUM=$(git rev-list head | sort | wc -l)
#GIT_COMMIT_ID=$(git rev-list head | head -1)
#echo "git version:${GIT_VER_NUM},commit id ${GIT_COMMIT_ID}"
PRO_DIR="${CURRENT_DIR}/xxx"
cd ${PRO_DIR}
WORKSPACE_DIR="${PRO_DIR}/*.xcworkspace"

export LC_ALL="en_US.UTF-8"
/usr/local/bin/pod install
PLIST_DIR="${PRO_DIR}/xxx/xxx-Info.plist"
echo "plist dir is $PLIST_DIR"

#打包前修改build号
/usr/libexec/PlistBuddy -c "Set CFBundleVersion ${GIT_VER_NUM}" ${PLIST_DIR}

APP_VERSION=$(/usr/libexec/PlistBuddy -c "print CFBundleVersion" ${PLIST_DIR})
APP_SHORT_VERSION=$(/usr/libexec/PlistBuddy -c "print CFBundleShortVersionString" ${PLIST_DIR})
echo "version ${APP_VERSION} build ${APP_SHORT_VERSION}"

RELEASE_IPA_DIR=${CURRENT_DIR}/IOS

#date +%Y%m%d%H%M%S
BUILD_DATE=$(date +%Y%m%d%H%M)
IPA_NAME="Release_${BUILD_DATE}_xxx_V${APP_SHORT_VERSION}.${APP_VERSION}.ipa"
DSYM_NAME="Release_${BUILD_DATE}_xxx_V${APP_SHORT_VERSION}.${APP_VERSION}.app.dSYM"

if [ -d ${RELEASE_IPA_DIR} ]; then
echo "${RELEASE_IPA_DIR} exist"
rm -rf ${RELEASE_IPA_DIR}/*.ipa
else
echo "${RELEASE_IPA_DIR} do not exist, need create dir"
mkdir ${RELEASE_IPA_DIR}
fi

CONFIGURATION_INHOUSE="Release"
SCHEME="xxx"

BUILD_APP_DIR="${PRO_DIR}/Build/Products/${CONFIGURATION_INHOUSE}-iphoneos"

xcodebuild clean -workspace $WORKSPACE_DIR -scheme "${SCHEME}" -configuration "${CONFIGURATION_INHOUSE}" -derivedDataPath "${PRO_DIR}"
xcodebuild -workspace $WORKSPACE_DIR -scheme "${SCHEME}" -configuration "${CONFIGURATION_INHOUSE}" -derivedDataPath "${PRO_DIR}"
xcrun -sdk iphoneos PackageApplication -v ${BUILD_APP_DIR}/*.app -o ${RELEASE_IPA_DIR}/${IPA_NAME}

OUTDIR="/Users/Shared/Jenkins/IOS"
BUILD_DATE=$(date +%Y%m%d)
OUT_IPA_DIR=${SEAFILEDIR}/${BUILD_DATE}
if [ -d ${OUT_IPA_DIR} ]; then
echo "${OUT_IPA_DIR} exist"
else
echo "${OUT_IPA_DIR} do not exist, need create dir"
mkdir ${OUT_IPA_DIR}
fi
mv ${RELEASE_IPA_DIR}/${IPA_NAME} ${OUT_IPA_DIR}
mv ${BUILD_APP_DIR}/*.dSYM ${OUT_IPA_DIR}/${DSYM_NAME}



###该处为非pod管理的工程关键代码
#BUILD_APP_DIR=${CURRENT_DIR}/xxx/build/${CONFIGURATION_INHOUSE}-iphoneos/*.app
#xcodebuild clean -project $PRO_DIR -target ${TARGET_NAME} -configuration ${CONFIGURATION_INHOUSE}
#xcodebuild -project $PRO_DIR -target ${TARGET_NAME} -configuration ${CONFIGURATION_INHOUSE}
#xcrun -sdk iphoneos PackageApplication -v ${BUILD_APP_DIR} -o ${OUT_IPA_DIR}/${IPA_NAME}

```

升级
---
前段时间将XCode升级到8.3之后，发现之前那的打包脚本被弃用了，顺便更新下脚本，思路不变，使用export命令生成ipa文件。
使用export命令之前，需要在工程目录添加plist文件，描述打包等相关信息。给个appstore包的plist文件例子。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>app-store</string>
</dict>
</plist>
```
inHouse证书的例子，只是将method的value改为enterprise即可。还有其他的值：ad-hoc，development等。上面的plist文件还可以添加其他的键值对，但用默认值即可。

>Xcode9.0后，archive需要指明描述文件，也就是上面的plist文件需要新增provisioningProfiles键值对。
>`需要将provisioningProfiles值的yourAppBundleId和对应的yourAppProvisioningProfileName改为你app的值`

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>enterprise</string>
    <key>provisioningProfiles</key>
    <dict>
      <key>yourAppBundleId</key>
      <string>yourAppProvisioningProfileName</string>
    </dict>
</dict>
</plist>
```

升级后的打包核心脚本，其中packageRelease.plist的内容与上面的例子类似

* 普通的工程

```shell
CURRENT_DIR=$(pwd)
PRO_DIR = ${CURRENT_DIR}
XC_PROJECT_DIR="${PRO_DIR}/*.xcodeproj"
CONFIGURATION_INHOUSE="Release"
SCHEME="xxx"
ARCHIVE_PATH="${PRO_DIR}/archivePath/${SCHEME}.xcarchive"
EXPORT_PATH="${PRO_DIR}/exportPath"
/usr/bin/security unlock-keychain  -p "jenkins" "/Users/Shared/Jenkins/Library/Keychains/login.keychain"

xcodebuild clean -project $XC_PROJECT_DIR -scheme ${SCHEME} -configuration "${CONFIGURATION_INHOUSE}" -archivePath "${ARCHIVE_PATH}"
xcodebuild -project $XC_PROJECT_DIR -scheme ${SCHEME} -configuration "${CONFIGURATION_INHOUSE}" -archivePath "${ARCHIVE_PATH}" archive
xcodebuild -exportArchive -archivePath "${ARCHIVE_PATH}" -exportOptionsPlist  "${PRO_DIR}/packageRelease.plist" -exportPath "${EXPORT_PATH}"
```

* 如果工程用了pod之类的管理

```shell
CURRENT_DIR=$(pwd)
PRO_DIR = ${CURRENT_DIR}
WORKSPACE_DIR="${PRO_DIR}/*.xcworkspace"
CONFIGURATION_INHOUSE="Release"
SCHEME="xxx"
ARCHIVE_PATH="${PRO_DIR}/archivePath/${SCHEME}.xcarchive"
EXPORT_PATH="${PRO_DIR}/exportPath"
/usr/bin/security unlock-keychain  -p "jenkins" "/Users/Shared/Jenkins/Library/Keychains/login.keychain"

xcodebuild clean -workspace ${WORKSPACE_DIR} -scheme "${SCHEME}" -configuration "${CONFIGURATION_INHOUSE}" -archivePath "${ARCHIVE_PATH}"
xcodebuild -workspace ${WORKSPACE_DIR} -scheme "${SCHEME}" -configuration "${CONFIGURATION_INHOUSE}" -archivePath "${ARCHIVE_PATH}" archive
xcodebuild -exportArchive -archivePath "${ARCHIVE_PATH}" -exportOptionsPlist  "${PRO_DIR}/packageRelease.plist" -exportPath "${EXPORT_PATH}"
```

第三方
---
推荐使用  [fastlane](https://github.com/fastlane/fastlane/)
