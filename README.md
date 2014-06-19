Django External Schema Evolution Branch
=======================================

This project allows you to make Django model changes without having to
drop and re-add tables.  This project started as a Google Summer
of Code project in 2006.

Quick Start
===========
To start using deseb, add the `import deseb` line after you set `DJANGO_SETTINGS_MODULE`
in your `manage.py`.  Like this:

```
#!/usr/bin/env python
import os
import sys

if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")
    import deseb
    from django.core.management import execute_from_command_line

    execute_from_command_line(sys.argv)
```


