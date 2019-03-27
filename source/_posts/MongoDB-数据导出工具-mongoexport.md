title: MongoDB 数据导出工具 mongoexport
date: 2019-03-27 11:11:51
tags:
- MongoDB
categories:
- 数据库
- MongoDB
---

导出示例：

    $ mongoexport --host 192.168.72.60 --db realtime_statistic_backup --collection all_play_stats_summary --fields "_id.timestamp,total_uv" --type csv --out uv.csv --sort "{'_id.timestamp': -1}" --query "{'_id.province_id':'sc','_id.city_id':'all', '_id.display_name': 'all'}"

<!-- more -->

- 3.4.6 版本后支持 --uri 方式连接 MongoDB
- --fields 指定要导出的 field，如果是嵌套的 json，可以通过点（.）操作指定内层的 field
