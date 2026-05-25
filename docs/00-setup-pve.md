# Setup pve
Add in `/etc/network/interfaces`
```
auto isp_hq
iface isp_hq inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0

auto isp_br
iface isp_br inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0

auto hq_lan
iface hq_lan inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0

auto br_lan
iface br_lan inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0
```