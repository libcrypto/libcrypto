---
title: "SSTI_1"
date: 2020-01-04T18:08:50+04:00
draft: true
toc: false
images:
tags: [bugbounty,SSTI]
---

While checking different web bugs Server-Side Template Injection, or (SSTI) for short, caught my attention. Maybe cuz they can lead to RCE (Remote Code Execution), so naturally will get a high rating. And, sometimes it involves python which I like too. Let's dwell on this type of vulnerability a little.

## What is a template engine?

Web apps use template engines to ease the process of populating web pages with dynamic content. Take this simple script for example

```python
import sys
from jinja2 import Template

template = Template("""
    <!DOCTYPE html>
    <html>
    <p>Hello {{ username }}</p>
    </body>
    </html>}}
    """)

print (template.render(username=sys.argv[1]))
```


If we run it like below we get content  that uses provided input

```bash
$ python ssti.py Friend

    <!DOCTYPE html>
    <html>
    <p>Hello Friend</p>
    </body>
    </html>}}
```