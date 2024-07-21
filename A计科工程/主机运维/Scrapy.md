---
layout: note
---

## 简单入门

第一步新建一个项目:

```bash
scrapy startproject tutorial
```

然后就会生成一个tutorial的目录，包含以下内容：

```
tutorial/
    scrapy.cfg            # deploy configuration file

    tutorial/             # project's Python module, you'll import your code from here
        __init__.py

        items.py          # project items definition file

        middlewares.py    # project middlewares file

        pipelines.py      # project pipelines file

        settings.py       # project settings file

        spiders/          # a directory where you'll later put your spiders
            __init__.py
```

Scrapy
