title: "Quartz Cron Expression"
date: 2013-01-11
tags: Quartz
---

## 表达式格式及字段说明

Quartz 定时表达式一般有6个字段单位（也可7个），使用空格间隔。从前到后按照如下的格式：
秒 分 时 日 月 星期[ 年] （最后一个单位“年”是可选的）。

<!--
| 字段        |允许值                                             | 可用特殊字符      |
| ----------- |---------------------------------------------------| ----------------- |
| 秒          |0-59                                               | , – * /           |
| 分          |0-59                                               | , – * /           |
| 时          |0-23                                               | , – * /           |
| 日          |1-31                                               | , – * ? / L W C   |
| 月          |1-12                                               | , – * /           |
| 星期        |1-7(1=SUN) 或 SUN，MON，TUE，WED，THU，FRI，SAT    | , – * ? / L C #   |
| 年(可选)    |1970－2099                                         | , – * /           | -->

<table border="1"><tbody><tr><th>字段</th><th>允许值</th><th>可用特殊字符</th></tr><tr><td>秒</td><td>0-59</td><td>, - * /</td></tr><tr><td>分</td><td>0-59</td><td>, - * /</td></tr><tr><td>时</td><td>0-23</td><td>, - * /</td></tr><tr><td>日</td><td>1-31</td><td>, - * ? / L W C</td></tr><tr><td>月</td><td>1-12</td><td>, - * /</td></tr><tr><td>星期</td><td>1-7(1=SUN) 或 SUN，MON，TUE，WED，THU，FRI，SAT</td><td>, - * ? / L C #</td></tr><tr><td>年(可选)</td><td>1970－2099</td><td>, - * /</td></tr></tbody></table>

<!--more-->

## 特殊字符含义

**\*** : 对应于该时间域中的每一个值，也就是任何时刻。例如 在秒字段，表示每秒钟。

**?** : 只能用于天和星期字段，表示不指定值。当其中一个字段指定了值以后，未避免冲突，另一个值需指定为 ? 。

**\-** : 表示一个范围，例如 时字段指定 1-3，表示 1点，2点，3点 。

**,** : 表示一个列表值，例如 时字段指定 1,5,6 表示 1点，5点，6点。

**/** : 表示一个等长序列。如果在分钟字段指定 0/15，则表示为 0,15,30,45分，而5/15则表示 5,20,35,50秒。*/15等同于 0/15。

**L** : 说明了某域上允许的最后一个值。它仅被日和周域支持。当用在日域上，表示的是在月域上指定的月份的最后一天。例如，当月域上指定了JAN 时，在日域上的L会促使 trigger 在1月31号被触发。假如月域上是SEP，那么 L 会预示着在9月30号触发。换句话说，就是不管指定了哪个月，都是在相应月份的时最后一天触发 trigger。

表达式 0 0 8 L * ? 意义是在每个月最后一天的上午 8:00 触发 trigger。在月域上的 * 说明是 "每个月"。

当 L 字母用于周域上，指示着周的最后一天，就是星期六 (或者数字7)。所以如果你需要在每个月的最后一个星期六下午的 11:59 触发 trigger，你可以用这样的表达式0 59 23 ? * L。

当使用于周域上，你可以用一个数字与 L 连起来表示月份的最后一个星期 X。例如，表达式 0 0 12 ? * 2L 说的是在每个月的最后一个星期一触发 trigger。

虽然你能用星期数(1-7)与 L 连用，但是不允许你用一个范围值和列表值与 L 连用。这会产生不可预知的结果。

**W** : 代表着平日 (Mon-Fri),也就是工作日（周一到周五），并且仅能用于日域中。它用来指定离指定日的最近的一个平日。大部分的商业处理都是基于工作周的，所以 W 字符可能是非常重要的。例如，日域中的15W 意味着 "离该月15号的最近一个平日。" 假如15号是星期六，那么 trigger 会在14号(星期五)触发，因为星期四比星期一（这个例子中是17号）离15号更近。（译者Unmi注：不会在17号触发的，如果是15W，可能会是在14号(15号是星期六)或者15号(15号是星期天)触发，也就是只能出现在邻近的一天，如果15号当天为平日直接就会当日执行）。W只能用在指定的日域为单天，不能是范围或列表值。

**#** : 仅能用于周域中，它用于指定月份中的第几周的哪一天。例如，如果你指定周域的值为6#3，它意思是某月的第三个周五 (6=星期五，#3意味着月份中的第三周)。另一个例子2#1意思是某月的第一个星期一 (2=星期一，#1意味着月份中的第一周)。注意，假如你指定#5，然而月份中没有第 5 周，那么该月不会触发。

## Quartz 在 Spring 中的应用

俺都是在Spring中使用 quartz的，从来没有单独使用过。Spring 简化了 Quartz 的使用，通过简单的配置，就能达到定时出发某个类的某个方法的目的。在一个简单的配置中包括 触发器。触发器配置要包括作业内容和作业触发条件2部分内容，作业触发条件配置作业的开始结束时间或处理频度，而作业内容指定了要处理某个类的某个方法。如下面的配置：

``` java
<bean id="trigger1" class="org.springframework.scheduling.quartz.CronTriggerBean">
    <property name="jobDetail">
        <bean class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
            <property name="targetObject" ref="simpleService" />
            <property name="targetMethod" value="testMethod1" />
        </bean>
    </property>
    <property name="cronExpression" value="0/5 * * * * ?" />
</bean>
```

配置好触发器后，将触发器加入到quartz的任务列表就可以了，如下：

``` java
<bean name="quartzScheduler"
    class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
    <property name="triggers">
        <list>
            <ref bean="trigger1" />
        </list>
    </property>
</bean>
```

<span style="line-height: 1.6em;">更多配置代码可参看我在github上的代码：</span>

[https://github.com/buzheng/buzheng-study-java/blob/master/src/main/java/org/buzheng/study/quartz/applicationContext-schedule.xml](https://github.com/buzheng/buzheng-study-java/blob/master/src/main/java/org/buzheng/study/quartz/applicationContext-schedule.xml)

参考：
http://blog.csdn.net/lvshow/article/details/6952400
http://biaoming.iteye.com/blog/39532