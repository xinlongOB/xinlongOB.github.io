---
title: zabbix配置微信告警
tags:
  - linux
  - zabbix
categories: 运维
date: 2020-06-30 18:54:52
---
## 首先需要申请一个企业号
申请企业号，需要一个绑定你本人开户银行卡的微信号。
<br/>申请网址 https://qy.weixin.qq.com/<br/>
点击“立即注册”。
<br/>根据提示注册企业号，到“选择类型”时，选择最右边的企业号。<br/>
注意：企业描述中：“报警”是敏感词不能使用。
<br/>登录之后，可以看到如下页面<br/>
![](../1.png)
<br/>按照下图依次点击。<br/>
![](../2.png)
<br/>![](../3.png)<br/>
![](../4.png)

## 关注企业号的方法
点击左侧的“设置”-二维码，使用微信扫一扫扫描二维码
<br/>![](../5.png)<br/>
点击左侧列的“应用中心”，点击“我的应用”下面的加号
<br/>![](../6.png)<br/>
填写应用名称，描述。一切正常的话，点击进入刚才创建的应用
<br/>![](../7.png)<br/>
这里的应用 id 号需要记住。后面需要填写

## 设置管理员
设置-功能设置-权限管理-新建管理组
<br/>![](../8.png)<br/>
![](../9.png)
<br/>![](../10.png)<br/>
注意：这里要记录下来下面的 CorpID 和 Secret。

## 编写脚本
在/usr/lib/zabbix/alertscripts目录(配置文件定义)下新建一个名为 wechat.sh 的脚本文件
```bash
#!/bin/bash
CropID='ww13d3c1c55e5d3414'   # 企业id-在网页应用管理可以查到
Secret='-qo7YckISjsL11u8kI5PF0gGJrjYKlk0ISF2ftAPuzQ'   # SecretID-在网页应用管理可以查到
GURL="https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=$CropID&corpsecret=$Secret"
Gtoken=`/usr/bin/curl -s -G $GURL | awk -F'access_token":"' '{print $2}'|awk -F'"' '{print $1}' `
PURL="https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=$Gtoken"
function body() {
local int AppID=1000002  # 应用id-在网页应用管理可以查到
local UserID=$1
local PartyID=1
local Msg=$(echo "$@" | cut -d" " -f3-)
printf '{\n'
printf '\t"touser": "'"$User"\"",\n"
printf '\t"toparty": "'"$PartyID"\"",\n"
printf '\t"msgtype": "text",\n'
printf '\t"agentid": "'" $AppID "\"",\n"
printf '\t"text": {\n'
printf '\t\t"content": "'"$Msg"\""\n"
printf '\t},\n'
printf '\t"safe":"0"\n'
printf '}\n'
}
/usr/bin/curl --data-ascii "$(body $1 $2 $3)" $PURL
```
需要设置权限不然调用的时候会报错没有权限
```bash
sudo  chown zabbix:zabbix   wechat.sh  -R 
sudo  chmod +x     wechat.sh
```
执行./wechat.sh 1 1 test 看自己微信是否能收到信息，如果能的话，继续下一步。反之检查上面有什么问题。

## zabbix后台配置
管理—示警介类型—创建媒体类型
<br/>创建报警媒介类型 (脚本参数分别对应：收件人地址、主题、详细内容)<br/>
![](../21.png)
<br/>配置用户 选择admin用户<br/>
![](../22.png)
<br/>添加报警媒介<br/>
![](../23.png)
<br/>创建报警动作 配置-动作-创建动作,新建动作<br/>
![](../24.png)
<br/>新建操作<br/>
![](../25.png)
<br/>![](../26.png)<br/>

```bash
操作

	故障{TRIGGER.STATUS},服务器:{HOSTNAME1}发生: {TRIGGER.NAME}故障!
	
	告警主机:{HOSTNAME1}
	告警时间:{EVENT.DATE} {EVENT.TIME}
	告警等级:{TRIGGER.SEVERITY}
	告警信息: {TRIGGER.NAME}
	告警项目:{TRIGGER.KEY1}
	问题详情:{ITEM.NAME}:{ITEM.VALUE}
	当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}
	事件 ID:{EVENT.ID}
```

添加恢复操作
<br/>![](../27.png)<br/>

```bash
恢复操作

	恢复{TRIGGER.STATUS}, 服务器:{HOSTNAME1}: {TRIGGER.NAME}已恢复!
	
	告警主机:{HOSTNAME1}
	告警时间:{EVENT.DATE} {EVENT.TIME}
	告警等级:{TRIGGER.SEVERITY}
	告警信息: {TRIGGER.NAME}
	告警项目:{TRIGGER.KEY1}
	问题详情:{ITEM.NAME}:{ITEM.VALUE}
	当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}
	事件 ID:{EVENT.ID}
```

配置完成后测试(修改触发器或者关闭进程)
<br/>![](../28.png)<br/>
![](../29.png)
<br/>![](../30.png)<br/>

## 邮件内容以及在动作日志中查看发送记录
![](../35.png)