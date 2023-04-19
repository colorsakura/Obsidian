---
Date: 2022-12-13 22:20
Tag: TODO
---

```
ip rule add fwmark 1 table 100
ip route add local 0.0.0.0/0 dev lo table 100
```

