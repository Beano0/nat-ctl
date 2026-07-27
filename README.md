# nat-ctl

NAT and firewall rule manager for Proxmox hosts. Started life as `net-mgr`, renamed once it became a proper part of my homelab/production toolbox.

Manages iptables NAT rules - port forwards, masquerade, SNAT — without hand-editing iptables every time I spin up or move a VM. State lives in a flat config file so it's easy to inspect, back up, and reason about instead of trusting whatever's currently loaded in the kernel.

## Why

Running a multi-region setup (Proxmox host + several VMs behind NAT) meant I was repeating the same iptables commands constantly and occasionally screwing them up. This turns that into a small set of commands with one source of truth.

## Usage

```
nat-ctl --reload                          reload all rules from config
nat-ctl --list                            show current rules with IDs
nat-ctl --delete <id>                     remove a rule
nat-ctl --fwd <ip> --p <port> --t <tag>   port forward to a VM
nat-ctl --masq <subnet>                   masquerade a subnet
nat-ctl --snat <subnet> --to <ip>         static NAT for a subnet
```

Rules are stored as pipe-delimited records in `/etc/nat-ctl/rules.conf`, each with a numbered ID so they're easy to reference and delete.

## Install

```
sudo install -m 755 nat-ctl /usr/local/sbin/nat-ctl
sudo install -m 644 nat-ctl.service /etc/systemd/system/nat-ctl.service
sudo systemctl daemon-reload
sudo systemctl enable --now nat-ctl.service
```

First run creates `/etc/nat-ctl/rules.conf` automatically. Add rules with `--fwd`/`--masq`/`--snat`, `--list` to check them, `--reload` to reapply everything on demand.