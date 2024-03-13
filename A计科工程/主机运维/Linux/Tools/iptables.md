---
layout: note
---

53 -> 9053

```bash
iptables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 9053
iptables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 9053
```
