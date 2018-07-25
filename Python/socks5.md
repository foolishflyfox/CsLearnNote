---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 使用 socks5 代理抓包

```python
from sockshandler import SocksiPyHandler
opener = urllib.request.build_opener(
    SocksiPyHandler(socks.SOCKS5, '127.0.0.1', 1080))
facebook = opener.open('https://www.facebook.com')
facebook_html = facebook.read()
```
