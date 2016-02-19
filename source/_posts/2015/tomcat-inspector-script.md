title: "Tomcat 监测脚本"
date: 2015-10-29
tags: [Tomcat,Java,Shell]
---

通过 Tomcat 监测脚本 按照一定的时间频度来检测Tomcat应用是否正常，如果进程不存在，则启动 Tomcat；如果进程存在而访问不正常，则杀掉进程，再启动 Tomcat。 <!--more-->

## 脚本的主体

```bash
vi /home/dev/ctl/tomcat-inspector/image.sh

#!/bin/bash

echo $(date '+%Y-%m-%d %H:%M:%S')
whoami

JAVA_HOME=/opt/java/jdk1.8.0_40
PATH=$JAVA_HOME/bin:$PATH
export PATH JAVA_HOME

port=8086
sn="tomcat-image-$port"
th="/home/dev/tomcat/$sn"
url="http://you-app-server/"

tomcat=`ps -ef | grep java | grep $sn | grep $port | wc -l`

if [ $tomcat -eq 0 ]
then
    echo $sn stoped.
    echo starting $sn ......
    $th/bin/startup.sh
fi

if [ $tomcat -ge 1 ]
then
    tpid=`ps -ef | grep java | grep $sn | grep $port | awk '{print $2}'`
    echo $sn is running, pid = $tpid

    httpStatus=`curl -I $url 2>/dev/null | grep HTTP | awk '{print $2}'`
    echo $httpStatus
    if [[ -z "$httpStatus" || $httpStatus -ge 500 ]]
    then
        echo killing $sn ......
        kill -9 $tpid

        echo starting $sn ......
        $th/bin/startup.sh
    fi
fi
```

## 设定执行频度

```shell
crontab -e
*/5 * * * * /home/dev/ctl/tomcat-inspector/image.sh >> /home/dev/ctl/tomcat-inspector/logs/image.log 2>&1
```

Done.
