# SHELL 笔记

### 日期格式化

- 获得当天的日期 date +%Y-%m-%d
- 前一天的日期 $(date --date='1 days ago +%Y%m%d')
- 前两天的日期 $(date --date='2 days ago +%Y%m%d') 
- 获取明天的日期 date -d next-day +%Y%m%d
- 获取昨天的日期 date -d last-day +%Y%m%d
- 获取上个月的年和月 date -d last-month +%Y%m
- 获取下个月的年和月 date -d next-month +%Y%m
- 获取明年的年份 date -d next-year +%Y

### 逻辑运算符

- 逻辑与 &&
- 逻辑或 ||
- 逻辑否 !

注意在 if 条件中使用时应使用双[[]] 

```shell
if [[ -z "$httpStatus" || $httpStatus -ge 500 ]]
```
