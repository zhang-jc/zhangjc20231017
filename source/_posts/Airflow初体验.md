---
title: Airflow初体验
date: 2021-12-21 16:29:43
tags:
- 大数据
- 调度系统
- Airflow
categories:
- 大数据
- Airflow
---

# 指定Airflow版本安装

```Shell
> pip install apache-airflow===1.10.3
> airflow initdb
```

# 遇到的问题
## No such revision or branch '7b2661a43ba3'

```Log
frin-mac:~ zhangjc$ airflow initdb
[2021-12-21 17:01:31,062] {__init__.py:51} INFO - Using executor SequentialExecutor
DB: sqlite:////Users/zhangjc/airflow/airflow.db
[2021-12-21 17:01:31,779] {db.py:350} INFO - Creating tables
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
Traceback (most recent call last):
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/base.py", line 143, in _catch_revision_errors
    yield
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/base.py", line 336, in _upgrade_revs
    revs = list(revs)
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/revision.py", line 645, in _iterate_revisions
    requested_lowers = self.get_revisions(lower)
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/revision.py", line 299, in get_revisions
    return sum([self.get_revisions(id_elem) for id_elem in id_], ())
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/revision.py", line 299, in <listcomp>
    return sum([self.get_revisions(id_elem) for id_elem in id_], ())
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/revision.py", line 304, in get_revisions
    for rev_id in resolved_id)
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/revision.py", line 304, in <genexpr>
    for rev_id in resolved_id)
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/alembic/script/revision.py", line 362, in _revision_for_ident
    resolved_id)
alembic.script.revision.ResolutionError: No such revision or branch '7b2661a43ba3'
```

解决方法：删除 ${AIRFLOW_HOME} 下的 airflow.db，然后再试。

## invalid literal for int() with base 10: '30.0'

```Log
ERROR [airflow.models.DagBag] invalid literal for int() with base 10: '30.0'
Traceback (most recent call last):
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/airflow/models/__init__.py", line 584, in collect_dags
    safe_mode=safe_mode)
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/airflow/models/__init__.py", line 411, in process_file
    with timeout(configuration.conf.getint('core', "DAGBAG_IMPORT_TIMEOUT")):
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/airflow/configuration.py", line 308, in getint
    return int(self.get(section, key, **kwargs))
ValueError: invalid literal for int() with base 10: '30.0'
```

如果使用Python2安装会出现这个错误，请使用Python3.6。

## 包冲突

安装过程会出现以下包冲突：

```Log
flask 1.1.4 has requirement itsdangerous<2.0,>=0.24, but you'll have itsdangerous 2.0.1 which is incompatible.
flask 1.1.4 has requirement Jinja2<3.0,>=2.10.1, but you'll have jinja2 2.10 which is incompatible.
flask 1.1.4 has requirement Werkzeug<2.0,>=0.15, but you'll have werkzeug 0.14.1 which is incompatible.
pendulum 1.4.4 has requirement tzlocal<2.0.0.0,>=1.5.0.0, but you'll have tzlocal 4.1 which is incompatible.
```

包冲突会导致Airflow安装失败，执行airflow命令出现异常：

```Shell
# airflow
Traceback (most recent call last):
  File "/opt/Python3.6.15/bin/airflow", line 21, in <module>
    from airflow import configuration
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/__init__.py", line 40, in <module>
    from flask_admin import BaseView
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask_admin/__init__.py", line 6, in <module>
    from .base import expose, expose_plugview, Admin, BaseView, AdminIndexView  # noqa: F401
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask_admin/base.py", line 6, in <module>
    from flask import Blueprint, current_app, render_template, abort, g, url_for
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask/__init__.py", line 21, in <module>
    from .app import Flask
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask/app.py", line 69, in <module>
    from .wrappers import Request
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask/wrappers.py", line 14, in <module>
    from werkzeug.wrappers.json import JSONMixin as _JSONMixin
ModuleNotFoundError: No module named 'werkzeug.wrappers.json'; 'werkzeug.wrappers' is not a package
```

### flask依赖包冲突解决

并安装flask 1.0.4版本。

```Shell
pip install flask===1.0.4
```

## No module named 'pysqlite2'

```Log
# airflow
Traceback (most recent call last):
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/dialects/sqlite/pysqlite.py", line 338, in dbapi
    from pysqlite2 import dbapi2 as sqlite
ModuleNotFoundError: No module named 'pysqlite2'

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/opt/Python3.6.15/bin/airflow", line 21, in <module>
    from airflow import configuration
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/__init__.py", line 44, in <module>
    settings.initialize()
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/settings.py", line 290, in initialize
    configure_orm()
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/settings.py", line 193, in configure_orm
    engine = create_engine(SQL_ALCHEMY_CONN, **engine_args)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/__init__.py", line 443, in create_engine
    return strategy.create(*args, **kwargs)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/strategies.py", line 87, in create
    dbapi = dialect_cls.dbapi(**dbapi_args)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/dialects/sqlite/pysqlite.py", line 343, in dbapi
    raise e
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/dialects/sqlite/pysqlite.py", line 341, in dbapi
    from sqlite3 import dbapi2 as sqlite  # try 2.5+ stdlib name.
  File "/opt/Python3.6.15/lib/python3.6/sqlite3/__init__.py", line 23, in <module>
    from sqlite3.dbapi2 import *
  File "/opt/Python3.6.15/lib/python3.6/sqlite3/dbapi2.py", line 27, in <module>
    from _sqlite3 import *
ModuleNotFoundError: No module named '_sqlite3'
```

安装sqlite-devel并重新安装Python：

```Shell
yum install sqlite-devel
make
make altinstall
```

## No module named 'wtforms.compat'

```Log
# airflow
[2021-12-22 09:46:47,645] {__init__.py:51} INFO - Using executor SequentialExecutor
Traceback (most recent call last):
  File "/opt/Python3.6.15/bin/airflow", line 22, in <module>
    from airflow.bin.cli import CLIFactory
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/bin/cli.py", line 67, in <module>
    from airflow.www.app import (cached_app, create_app)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www/app.py", line 37, in <module>
    from airflow.www.blueprints import routes
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www/blueprints.py", line 29, in <module>
    from airflow.www import utils as wwwutils
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www/utils.py", line 36, in <module>
    from wtforms.compat import text_type
ModuleNotFoundError: No module named 'wtforms.compat'
```

正解请参照：[https://stackoverflow.com/questions/69879246/no-module-named-wtforms-compat](https://stackoverflow.com/questions/69879246/no-module-named-wtforms-compat)

## Exception: Install 'email_validator' for email validation support

```Shell
/opt/Python3.6.15/lib/python3.6/site-packages/flask_sqlalchemy/__init__.py:852: UserWarning: Neither SQLALCHEMY_DATABASE_URI nor SQLALCHEMY_BINDS is set. Defaulting SQLALCHEMY_DATABASE_URI to "sqlite:///:memory:".
  'Neither SQLALCHEMY_DATABASE_URI nor SQLALCHEMY_BINDS is set. '
Traceback (most recent call last):
  File "/opt/Python3.6.15/bin/airflow", line 32, in <module>
    args.func(args)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/utils/cli.py", line 74, in wrapper
    return f(*args, **kwargs)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/bin/cli.py", line 1351, in create_user
    appbuilder = cached_appbuilder()
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/app.py", line 229, in cached_appbuilder
    cached_app(config=config, testing=testing)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/app.py", line 222, in cached_app
    app, _ = create_app(config, session, testing)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/app.py", line 82, in create_app
    from airflow.www_rbac.security import AirflowSecurityManager
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/security.py", line 23, in <module>
    from flask_appbuilder.security.sqla.manager import SecurityManager
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask_appbuilder/security/sqla/manager.py", line 8, in <module>
    from ..manager import BaseSecurityManager
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask_appbuilder/security/manager.py", line 11, in <module>
    from .views import AuthDBView, AuthOIDView, ResetMyPasswordView, AuthLDAPView, AuthOAuthView, AuthRemoteUserView, \
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask_appbuilder/security/views.py", line 17, in <module>
    from .forms import LoginForm_db, LoginForm_oid, ResetPasswordForm, UserInfoEdit
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask_appbuilder/security/forms.py", line 39, in <module>
    class RegisterUserDBForm(DynamicForm):
  File "/opt/Python3.6.15/lib/python3.6/site-packages/flask_appbuilder/security/forms.py", line 43, in RegisterUserDBForm
    email = StringField(lazy_gettext('Email'), validators=[DataRequired(), Email()], widget=BS3TextFieldWidget())
  File "/opt/Python3.6.15/lib/python3.6/site-packages/wtforms/validators.py", line 332, in __init__
    raise Exception("Install 'email_validator' for email validation support.")
Exception: Install 'email_validator' for email validation support.
```

安装 email-validator：

```Shell
 pip install email-validator
 ```

 ## no such table: ab_permission_view_role

异常信息如下：

```Log
Traceback (most recent call last):
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/base.py", line 1236, in _execute_context
    cursor, statement, parameters, context
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/default.py", line 536, in do_execute
    cursor.execute(statement, parameters)
sqlite3.OperationalError: no such table: ab_permission_view_role

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "/opt/Python3.6.15/bin/airflow", line 32, in <module>
    args.func(args)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/utils/cli.py", line 74, in wrapper
    return f(*args, **kwargs)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/bin/cli.py", line 1351, in create_user
    appbuilder = cached_appbuilder()
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/app.py", line 229, in cached_appbuilder
    cached_app(config=config, testing=testing)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/app.py", line 222, in cached_app
    app, _ = create_app(config, session, testing)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/app.py", line 182, in create_app
    security_manager.sync_roles()
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/security.py", line 456, in sync_roles
    self.create_custom_dag_permission_view()
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/utils/db.py", line 73, in wrapper
    return func(*args, **kwargs)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/airflow/www_rbac/security.py", line 404, in create_custom_dag_permission_view
    all_perm_views = set([role.permission_view_id for role in all_perm_view_by_user])
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/orm/query.py", line 3081, in __iter__
    return self._execute_and_instances(context)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/orm/query.py", line 3106, in _execute_and_instances
    result = conn.execute(querycontext.statement, self._params)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/base.py", line 980, in execute
    return meth(self, multiparams, params)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/sql/elements.py", line 273, in _execute_on_connection
    return connection._execute_clauseelement(self, multiparams, params)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/base.py", line 1099, in _execute_clauseelement
    distilled_params,
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/base.py", line 1240, in _execute_context
    e, statement, parameters, cursor, context
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/base.py", line 1458, in _handle_dbapi_exception
    util.raise_from_cause(sqlalchemy_exception, exc_info)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/util/compat.py", line 296, in raise_from_cause
    reraise(type(exception), exception, tb=exc_tb, cause=cause)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/util/compat.py", line 276, in reraise
    raise value.with_traceback(tb)
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/base.py", line 1236, in _execute_context
    cursor, statement, parameters, context
  File "/opt/Python3.6.15/lib/python3.6/site-packages/sqlalchemy/engine/default.py", line 536, in do_execute
    cursor.execute(statement, parameters)
sqlalchemy.exc.OperationalError: (sqlite3.OperationalError) no such table: ab_permission_view_role [SQL: 'SELECT ab_permission_view_role.id AS ab_permission_view_role_id, ab_permission_view_role.permission_view_id AS ab_permission_view_role_permission_view_id, ab_permission_view_role.role_id AS ab_permission_view_role_role_id \nFROM ab_permission_view_role JOIN ab_permission_view ON ab_permission_view.id = ab_permission_view_role.permission_view_id JOIN ab_view_menu ON ab_view_menu.id = ab_permission_view.view_menu_id \nWHERE ab_permission_view_role.role_id = ? AND ab_permission_view.view_menu_id != ?'] [parameters: (4, 51)] (Background on this error at: http://sqlalche.me/e/e3q8)
```

 正解参见：[https://stackoverflow.com/questions/57068702/sqlite3-raised-an-error-after-running-airflow-command-line](https://stackoverflow.com/questions/57068702/sqlite3-raised-an-error-after-running-airflow-command-line)