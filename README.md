> **Note** — The folder `linguist-samples/` contains tiny real files so GitHub can correctly display all languages used in this repo.  
> The actual content and examples remain in this README.

![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Find SQL Object Ownership Across All Databases With SQL
**Post Date: March 22, 2018**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

![Get Object Ownership Across Databases]( https://mikesdatawork.files.wordpress.com/2018/03/image0013.png "Get Object Ownership With SQL")
 
<p>Whenever you're clearing out your database servers and removing disabled, or expired accounts; you'll need to check to see if there is any standing object ownership on some of those accounts. This way you can transfer ownership before removing the old accounts.
Here is some SQL logic to show all the object ownership across all databases. This excludes the existing defaults such as 'dbo', 'sys', or 'information_schema' owners.</p>    
  
## SQL-Logic
```SQL
use master;
set nocount on
 
if object_id('tempdb..#sodesc') is not null drop table  #sodesc
create table    #sodesc ([xtype] varchar(3), [desc] varchar(255))
insert into #sodesc select substring([name], 0, charindex(':', [name], 0)), right([name], charindex(':', reverse([name])) - 1) 
from master..[spt_values] where [name] like '%:%'
 
if object_id('tempdb..#ownership') is not null drop table #ownership
create table    #ownership ([database] varchar(255), [user_name] varchar(255), [object_name] varchar(255), [object_type] varchar(255))
 
declare @get_all_object_ownership   varchar(max)
set @get_all_object_ownership   =
'
use [?]; 
select
    db_name()
,   user_name([uid])
,   [name]
,   sod.[desc]
from
    [?]..sysobjects  so join #sodesc sod on so.[xtype] = sod.[xtype] collate SQL_Latin1_General_CP1_CI_AS
where
    user_name([uid]) not in (''sys'', ''INFORMATION_SCHEMA'', ''dbo'')
    and db_name() not in (''tempdb'')
order by
    user_name([uid])
,   [name]
'
insert into #ownership
exec master..sp_msforeachdb @get_all_object_ownership 
 
select * from #ownership
```


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

 
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")
