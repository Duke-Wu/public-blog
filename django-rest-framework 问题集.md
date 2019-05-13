### 1. [How to disable admin-style browsable interface of django-rest-framework?](https://stackoverflow.com/questions/11898065/how-to-disable-admin-style-browsable-interface-of-django-rest-framework)

> I am using [django-rest-framework](http://django-rest-framework.org/). It provides an awesome Django admin style browsable self-documenting API. But anyone can visit those pages and use the interface to add data (POST). How can I disable it?



You just need to remove the browsable API renderer from your list of supported renderers for the view.

You can do this globally like so:

```
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': (
        'rest_framework.renderers.JSONRenderer',
    )
}
```

Or on a per-view basis like so:

```
class MyView(...):
    renderer_classes = [renderers.JSONRenderer]
```

**Aside**: In many cases I think it's a shame that folks would choose to disable the browsable API in any case, as it's a big aid to any developers working on the API, and it *doesn't give them more permissions that they would otherwise have*. I can see that there might be business reasons for doing so in *some* cases, but generally I'd consider it a huge asset.



### 2. [Django MySQL error when creating tables](https://stackoverflow.com/questions/28561458/django-mysql-error-when-creating-tables)

I am building a django app with a MySQL DB. When I run 'python manage.py migrate' for the first time, some tables are created well then some errors appear. The error brought out is:

> django.db.utils.IntegrityError: (1215, 'Cannot add foreign key constraint')

When I run this MySQL command -

> SHOW ENGINE INNODB STATUS\G,

I get this >>>

```
2015-02-17 14:33:17 7f10891cf700 Error in foreign key constraint of table movie_store/#sql-4f1_66:
 FOREIGN KEY (`group_id`) REFERENCES `auth_group` (`id`):
Cannot resolve table name close to:
 (`id`)
```

The complete traceback is:

```
Creating tables...
    Creating table users
    Creating table merchant
    Creating table celery_taskmeta
    Creating table celery_tasksetmeta
    Creating table djcelery_intervalschedule
    Creating table djcelery_crontabschedule
    Creating table djcelery_periodictasks
    Creating table djcelery_periodictask
    Creating table djcelery_workerstate
    Creating table djcelery_taskstate
    Creating table post_office_email
    Creating table post_office_log
    Creating table post_office_emailtemplate
    Creating table post_office_attachment
    Running deferred SQL...
Traceback (most recent call last):
  File "manage.py", line 10, in <module>
    execute_from_command_line(sys.argv)
  File "/usr/local/lib/python2.7/dist-packages/django/core/management/__init__.py", line 338, in execute_from_command_line
    utility.execute()
  File "/usr/local/lib/python2.7/dist-packages/django/core/management/__init__.py", line 330, in execute
    self.fetch_command(subcommand).run_from_argv(self.argv)
  File "/usr/local/lib/python2.7/dist-packages/django/core/management/base.py", line 390, in run_from_argv
    self.execute(*args, **cmd_options)
  File "/usr/local/lib/python2.7/dist-packages/django/core/management/base.py", line 441, in execute
    output = self.handle(*args, **options)
  File "/usr/local/lib/python2.7/dist-packages/django/core/management/commands/migrate.py", line 173, in handle
    created_models = self.sync_apps(connection, executor.loader.unmigrated_apps)
  File "/usr/local/lib/python2.7/dist-packages/django/core/management/commands/migrate.py", line 309, in sync_apps
    cursor.execute(statement)
  File "/usr/local/lib/python2.7/dist-packages/django/db/backends/utils.py", line 80, in execute
    return super(CursorDebugWrapper, self).execute(sql, params)
  File "/usr/local/lib/python2.7/dist-packages/django/db/backends/utils.py", line 65, in execute
    return self.cursor.execute(sql, params)
  File "/usr/local/lib/python2.7/dist-packages/django/db/utils.py", line 95, in __exit__
    six.reraise(dj_exc_type, dj_exc_value, traceback)
  File "/usr/local/lib/python2.7/dist-packages/django/db/backends/utils.py", line 63, in execute
    return self.cursor.execute(sql)
  File "/usr/local/lib/python2.7/dist-packages/django/db/backends/mysql/base.py", line 124, in execute
    return self.cursor.execute(query, args)
  File "/usr/lib/python2.7/dist-packages/MySQLdb/cursors.py", line 174, in execute
    self.errorhandler(self, exc, value)
  File "/usr/lib/python2.7/dist-packages/MySQLdb/connections.py", line 36, in defaulterrorhandler
    raise errorclass, errorvalue
django.db.utils.IntegrityError: (1215, 'Cannot add foreign key constraint')
```

#### Answer

This will works

```
python manage.py migrate auth
python manage.py migrate
```

The issue because of other migration run before the auth, so this will make sure "authtools"'s migration run first