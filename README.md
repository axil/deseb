Django External Schema Evolution Branch
=======================================

This project allows you to make Django model changes without having to
drop and re-add tables.  This project started as a Google Summer
of Code project in 2006.

Quick Start
===========
To start using deseb, add the `import deseb` line after you set `DJANGO_SETTINGS_MODULE`
in your `manage.py`.  Like this:

```python
#!/usr/bin/env python
import os
import sys

if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")
    import deseb    # <-- ADD THIS LINE
    from django.core.management import execute_from_command_line

    execute_from_command_line(sys.argv)
```


How it Works
============
Let's say I have the following model:

```python
class Review(models.Model):
  when = models.DateTimeField(default=datetime.datetime.now)
  text = models.TextField(blank=True, null=True)
  rating = models.IntegerField(default=0)
  appt = models.ForeignKey(Appointment)
  user = models.ForeignKey(User)
  uuid = UUIDField(version=4)
```

And I want to add a new column `response`:

```python
class Review(models.Model):
  when = models.DateTimeField(default=datetime.datetime.now)
  text = models.TextField(blank=True, null=True)
  response = models.TextField(blank=True, null=True)
  rating = models.IntegerField(default=0)
  appt = models.ForeignKey(Appointment)
  user = models.ForeignKey(User)
  uuid = UUIDField(version=4)
```

If I run `./manage.py evolvedb`, I'll see:

```sql
$ ./manage.py evolvedb
appthub: the following schema upgrade is available:
ALTER TABLE `appthub_review` ADD COLUMN `response` longtext;
do you want to run the preceeding commands?
type 'yes' to continue, or 'no' to cancel: yes
schema upgrade executed
```

Now suppose I changed my mind on what that column should be called - it should be `response_text`:

```python
class Review(models.Model):
  when = models.DateTimeField(default=datetime.datetime.now)
  text = models.TextField(blank=True, null=True)
  response_text = models.TextField(blank=True, null=True, aka='response')
  rating = models.IntegerField(default=0)
  appt = models.ForeignKey(Appointment)
  user = models.ForeignKey(User)
  uuid = UUIDField(version=4)
```

Now I run `./manage.py evolvedb` again:

```sql
$ ./manage.py evolvedb
appthub: the following schema upgrade is available:
ALTER TABLE `appthub_review` CHANGE COLUMN `response` `response_text` longtext NULL;
do you want to run the preceeding commands?
type 'yes' to continue, or 'no' to cancel: yes
schema upgrade executed
```

Now supposed I thought this was a bad idea to begin with, so I change my model back to its original form:

```python
class Review(models.Model):
  when = models.DateTimeField(default=datetime.datetime.now)
  text = models.TextField(blank=True, null=True)
  rating = models.IntegerField(default=0)
  appt = models.ForeignKey(Appointment)
  user = models.ForeignKey(User)
  uuid = UUIDField(version=4)
```

Now run `./manage.py evolvedb` again:

```sql
$ ./manage.py evolvedb
appthub: the following schema upgrade is available:
-- warning: the following may cause data loss
ALTER TABLE `appthub_review` DROP COLUMN `response_text`;
-- end warning
do you want to run the preceeding commands?
type 'yes' to continue, or 'no' to cancel: yes
schema upgrade executed
```


