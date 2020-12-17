---
title: unity_packages
tags:
  - python
  - linux
date: 2020-12-11 14:40:48
---
```bash
#!/bin/sh

#参数判断  
if [ $# != 1 ];then
    echo "需要传入打包配置文件"  
    exit     
fi  

#Log颜色处理-------------start
function infolog()
{
    echo -e Log: "\033[32m$1\033[0m"
}

#输出报错log
function errorlog()
{
    echo -e Error: "\033[01;31m$1\033[0m"
}

#输出报错log并停止运行
function errorlogExit()
{
    echo -e Error: "\033[01;31m$1\033[0m"
    echo -e Error: "\033[01;31m打包失败\033[0m"
	exit
}
#Log颜色处理-------------end

#解析打包配置文件
CONFIG_PLIST=$1

# 版本号
CONFIG_Version=$(/usr/libexec/PlistBuddy -c 'Print :CFBundleShortVersionString' $CONFIG_PLIST)

# 打包格式文件
CONFIG_exportOptionsPlist=$(/usr/libexec/PlistBuddy -c 'Print :exportOptionsPlist' $CONFIG_PLIST)

# 是否为Il2cpp模式打包
CONFIG_isIl2cpp=$(/usr/libexec/PlistBuddy -c 'Print :isIl2cpp' $CONFIG_PLIST)

# build号
CFBundleVersion=$(/usr/libexec/PlistBuddy -c 'Print :buildVersion' $CONFIG_PLIST)

# 拼接游戏名称
ipa_name="xxxx_ios_publish_"${CFBundleShortVersionString}"."${CFBundleVersion}  

#UNITY程序的路径#
UNITY_PATH=/Applications/Unity/Unity.app/Contents/MacOS/Unity

#游戏程序路径 需要将本文件放入unity工程目录中#
PROJECT_PATH=$(dirname "$0")


#复制文件目录
COPY_FILE_PATH="要复制替换的文件路径"
COPY_FILE_PATH_PLIST=${COPY_FILE_PATH}/info.plist
COPY_FILE_PATH_XCODEPROJECT=${COPY_FILE_PATH}"/Unity-iPhone.xcodeproj"


#Xcode工程版本号
CFBundleShortVersionString=$CONFIG_Version

# 传给unity的XCODE导出路径
Xcode_Untiy_ProjectPath="build/iOS/XcodeProject"

#Xcode工程目录
Xcode_ProjectPath=${PROJECT_PATH}"/"${Xcode_Untiy_ProjectPath}

#将unity导出成xcode工程-----------------------------------start
	{
		#try 
		$UNITY_PATH -projectPath $PROJECT_PATH -executeMethod AutoBuild.BuildIos "Path_"${Xcode_Untiy_ProjectPath} -quit
	} || { 
		#catch
		errorlogExit "unity导出Xcode工程失败"
	}
	infolog "----------XCODE工程生成完毕,路径:"${Xcode_ProjectPath}
#将unity导出成xcode工程-----------------------------------end

#将复制文件拷贝到工程中-----------------------------------start
	{
		#try 
		
		infolog "----------开始复制/替换文件,替换文件路径:"${COPY_FILE_PATH}
		cp $COPY_FILE_PATH_PLIST ${Xcode_ProjectPath}
		cp -R $COPY_FILE_PATH_XCODEPROJECT ${Xcode_ProjectPath}
	} || { 
		#catch
		errorlogExit "复制资源失败"
	}
#将复制文件拷贝到工程中-----------------------------------end

#设置Xcode info.plist-----------------------------------start
	infolog "----------开始修改plist配置"
	{
		#try
		#修改plist中版本号
		#获取到info.plist
		PLIST_PATH=${Xcode_ProjectPath}/info.plist

		#使用PlistBuddy 修改plist中内容 注:PlistBuddy为绝对路径
		/usr/libexec/PlistBuddy -c 'Set :CFBundleShortVersionString '${CFBundleShortVersionString} $PLIST_PATH
		/usr/libexec/PlistBuddy -c 'Set :CFBundleVersion '${CFBundleVersion} $PLIST_PATH
	}||{
		#catch
		errorlogExit "修改plist内容失败"
	}
#设置Xcode info.plist-----------------------------------end

#开始打包ipa-----------------------------------start
	{
		#try
		#清理编译工程
		EXPORT_PATH="ipa包导出地址"
		xcodebuild clean -quiet

		cd $Xcode_ProjectPath
		#编译工程
		xcodebuild archive -scheme "Unity-iPhone" -configuration "Release" -archivePath  ${EXPORT_PATH}${ipa_name}"/xxxx_Archive.xcarchive" -quiet || exit

		#打包  -archivePath Xcode导出的文件路径   -configuration 导出格式(Debug/Release)   -exportPath ipa导出路径  -exportOptionsPlist 导出模式,使用plist文件控制 在xcode手动打包时会在ipa同目录下生成对应格式的plist文件 可以直接使用改pist文件并根据不同格式备份多份
		xcodebuild -exportArchive -archivePath ${EXPORT_PATH}${ipa_name}"/xxxx_Archive.xcarchive" -configuration "Release" -exportPath ${EXPORT_PATH}${ipa_name} -exportOptionsPlist ${CONFIG_exportOptionsPlist} -quiet || exit
	}||{
		#catch
		errorlogExit "xcode工程清理/打包/导出失败 请查看上层error log"
	}
#开始打包ipa-----------------------------------start

#复制字符表文件到导出ipa的文件夹------------------start
	if [ ! -d ${EXPORT_PATH}${ipa_name}"/xxxx_Archive.xcarchive/dSYMs" ]; then
		errorlog "----------dSYM字符表文件未导出,检查Xcode设置!!!!!!!!!"
	else
		cd ${EXPORT_PATH}${ipa_name}"/xxxx_Archive.xcarchive/dSYMs"
		if [ ! -e "xxxx.app.dSYM" ]; then
			#字符表文件不存在
			errorlog "----------dSYM字符表文件未导出,检查Xcode设置!!!!!!!!!"
		else
			cp -R "xxxx.app.dSYM" ${EXPORT_PATH}${ipa_name}
		fi
	fi
#复制字符表文件到导出ipa的文件夹------------------end

infolog "Ipa导出成功!!!!!!!!!"
#打开指定文件夹
open ${EXPORT_PATH}${ipa_name}
```