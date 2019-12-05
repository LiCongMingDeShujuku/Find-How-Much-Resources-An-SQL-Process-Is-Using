![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 查找SQL Process正在使用的资源量
#### Find How Much Resources An SQL Process Is Using
**发布-日期: 2015年07月14日 (评论)**

![#](images/##############?raw=true "#")

## Contents

- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


Here’s some sql logic to help you get the amount of resources any particular user process is taking. It will give you the following information per process.

这里是可以帮助你获得任何特定用户程序正在使用的资源量的一些sql逻辑（logic）。 它将为你提供每个程序的以下信息。

1.	System Name
2. User Name (user responsible for said process)
3. Program Name (program that initiated the process)
4. Time when started
5. Database Name
6. Session Id
7. Row Count (rows affected for this process)
8. CPU Time hh:mm:ss (amount of time the thread spent being processed)
9. Total Scheduled Time hh:mm:ss
10. Elapsed Time hh:mm:ss (duration of process)
11. Memory Usage (in kb)
12. Space Allocated for internal objects
13. Space Allocated for user objects
14. Space unallocated for internal objects
15. Space unallocated for user objects.


1. 系统名字
2. 用户名（用户对该流程负责）
3. 指令名称（指令启动该程序）
4. 开始时间
5. 数据库名称
6. 进程ID
7. 横数（受此进程影响的横）
8. CPU时间 小时：分钟：秒（线程处理所用的时间）
9. 总的预计时间 小时：分钟：秒
10. 经过的时间 小时：分钟：秒（线程的持续时间）
11. 所用内存（kb计量）
12. 为内部对象分配的空间
13. 为用户对象分配的空间
14. 内部对象未分配的空间
15. 用户对象未分配的空间

---
## Logic
```SQL
/*
Get the page space allocated with each process.
This is an excellent way to determine how many resources are being used in your database processes.
Note:
The counters track the total number of pages that have been allocated or deallocated for
tasks that are already completed in the session. The counters are updated only when a task
ends; they do not reflect running tasks. That 'status' will usually represent 'sleeping' as
the results of this query are from finished processes.

获取为每个进程分配的页面空间。
这是确定数据库进程使用了多少资源的绝佳方法。
注意：
计数器已经记录了线程中完成任务的已分配和取消分配的总空间页数。计数器仅在任务完成时更新，他们不记录正在运行的任务。查询已结束程序的状态一般是”睡眠（sleeping）”
更多信息请点这里：

More info can be found here:
https://msdn.microsoft.com/en-us/library/ms187938.aspx
*/
 
use master;
set nocount on
select
'system name' = sdes.host_name
, 'user name' = sdes.login_name
, 'program name' = sdes.program_name
, 'session started' = datename(dw, sdes.login_time) + ' ' + convert(char, sdes.login_time, 9)
, 'database' = db_name(sdes.database_id)
, 'session_id' = sdes.session_id
, 'row count' = sdes.row_count
, 'cpu time hh:mm:ss' = convert(varchar, dateadd(ms, sdes.cpu_time, 0), 114)
, 'total scheduled time hh:mm:ss' = convert(varchar, dateadd(ms, sdes.total_scheduled_time, 0), 114)
, 'elapsed time hh:mm:ss' = convert(varchar, dateadd(ms, sdes.total_elapsed_time, 0), 114)
, 'memory usage in kb' = (sdes.memory_usage * 8)
, 'space allocated - user objects in kb' = (sddssu.user_objects_alloc_page_count * 8)
, 'space allocated - internal objects in kb' = (sddssu.internal_objects_alloc_page_count * 8)
, 'space deallocated - user objects in kb' = (sddssu.user_objects_dealloc_page_count * 8)
, 'space deallocated - internal objects in kb' = (sddssu.internal_objects_dealloc_page_count * 8)
from
sys.dm_db_session_space_usage sddssu inner join sys.dm_exec_sessions sdes on sddssu.session_id = sdes.session_id
where
sdes.database_id > 4
and sdes.cpu_time > 0
--and sdes.is_user_process < 50
and sdes.is_user_process > 0
order by
sdes.login_time desc

```

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

