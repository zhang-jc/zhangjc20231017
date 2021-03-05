title: HiverServer2 启用 Ranger 行过滤规则后表 Insert Values 操作权限问题
date: 2021-03-05 17:46:11
tags:
- Hive
- Ranger
- 大数据
categories:
- 大数据
- Ranger
---

HiverServer2 启用 Ranger 行过滤规则后表 Insert Values 操作提示以下权限问题：

	Error: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [test] does not have [UPDATE] privilege on [dev/test_insert_table_values] (state=42000,code=40000)
	
默认的 Ranger 会拒绝启用行过滤规则表的 UPDATE 权限。Ranger 源代码如下（Ranger版本1.2.0）：类org.apache.ranger.authorization.hive.authorizer.RangerHiveAuthorizer中L1789到L1813

	private boolean isBlockAccessIfRowfilterColumnMaskSpecified(
			HiveOperationType hiveOpType, RangerHiveAccessRequest request) {
		boolean ret = false;
		RangerHiveResource resource = (RangerHiveResource) request.getResource();
		HiveObjectType objType = resource.getObjectType();

		if (objType == HiveObjectType.TABLE || objType == HiveObjectType.VIEW
				|| objType == HiveObjectType.COLUMN) {
			ret = hiveOpType == HiveOperationType.EXPORT;

			if (!ret) {
				if (request.getHiveAccessType() == HiveAccessType.UPDATE
						&& hivePlugin.BlockUpdateIfRowfilterColumnMaskSpecified) {
					ret = true;
				}
			}
		}

		if (LOG.isDebugEnabled()) {
			LOG.debug("isBlockAccessIfRowfilterColumnMaskSpecified(" + hiveOpType
					+ ", " + request + "): " + ret);
		}

		return ret;
	}

通过源代码跟踪找到解决方案：在ranger-hive-security.xml配置文件中设置xasecure.hive.block.update.if.rowfilter.columnmask.specified的值为false即可。如下：

	<property>
		<name>xasecure.hive.block.update.if.rowfilter.columnmask.specified</name>
		<value>false</value>
	</property>


