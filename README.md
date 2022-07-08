# ansible-dhcp-helper
This is an Ansible role for installing and configuring dhcp-helper (a DHCP/BOOTP relay agent).

dhcp-helper is a DHCP and BOOTP relay agent. It listens for DHCP and BOOTP broadcasts on
directly connected subnets and relays them to DHCP or BOOTP servers elsewhere. It also
relays replies from the remote servers back to partially configured hosts. Once hosts are
fully configured they can communicate directly with their servers and no longer  need the
services of a relay.

The only required  option is at least one DHCP server to relay to. The simplest way to
configure dhcp-helper on a router is just to give the interface to the network containing
the DHCP server in the `broadcast_interfaces` variable. All the other interfaces present on
the machine will then accept DHCP requests. On a machine which does not have an interface on
the network containing the DHCP server, use the `servers` variable instead.

## Example usage

Below is a sample Ansible Playbook that will use this role, with documentation of the
various possible options in-line.

None of the variables are mandatory, except you have to specify at least one server
(under servers) or one interface under broadcast_interfaces.

```yaml
---
- name: Deploy DHCP Helper
  hosts: dhcp-helpers
  become: yes

  vars:
    # Specify a DHCP or BOOTP server to relay to. The server may be given as a machine
    # name or dotted-quad IP address. More than one server may be specified.
    servers:
      - 192.0.2.123
      - 192.0.2.124
    # Relay to a DHCP or BOOTP server using broadcast via the specified interfaces.
    # This eliminates the need to give a server address. The interfaces are automatically
    # added to the list of interfaces which will not receive DHCP requests.
    broadcast_interfaces:
      - eth0
      - eth1
    # Specify which local interfaces to listen on for DHCP/BOOTP  broadcasts. If no
    # servers are specified, all interfaces are used except those specified in
    # exclude_interfaces and those specified in broadcast_interfaces.
    listen_interfaces:
      - eth2
      - eth3
    # Specify which local interfaces to exclude.
    exclude_interfaces:
      - eth4
      - eth5
    # Use alternative ports (1067/1068) for the DHCP client and server.
    alternative_ports: false

    # Specify an alternate path for dhcp-helper to record its  process-id in.
    # Normally /var/run/dhcp-helper.pid.
    pidfile: /tmp/my-pidfile
    
    # Specify the userid to which dhcp-helper will change after startup. The daemon must
    # normally be started as root, but it will drop root privileges after startup by
    # changing id to another user. Normally this user is "nobody" but that can be over-
    # ridden with this variable.
    user: somebody
  roles:
    - dhcp-helper
```

## Authors

Major parts of the README file have been lifted straight from the
[dhcp-helper(8) man page](https://manpages.ubuntu.com/manpages/focal/en/man8/dhcp-helper.8.html),
written by Simon Kelley <<simon@thekelleys.org.uk>>, who is also the author of dhcp-helper itself.

The Ansible role itself is written by Per von Zweigbergk <<pvz@pvz.pp.se>>.

## Notes

The Ansible role has only been tested on Raspberry Pi OS, but it's likely to work on any modern
Debian or Debian-derived Linux distribution, such as Ubuntu.

It will definitely not work in its current state on any other non-Debian-derived Linux distributions.

## Copying

The Ansible Role is available under the terms of the GNU General Public License Version 2, or any later version published by the Free Software Foundation.
