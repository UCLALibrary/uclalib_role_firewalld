# UCLALib Ansible Role: FirewallD [![Build Status](https://travis-ci.org/UCLALibrary/uclalib_role_firewalld.svg?branch=master)](https://travis-ci.org/UCLALibrary/uclalib_role_firewalld)

Configures firewalld using service and zone configuration files.

This role allows for the creation of custom services and rules using firewalld's rich language within a RHEL environment.

[RHEL7 FirewallD documentation](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Using_Firewalls.html)

[Rich Language documentation](https://fedoraproject.org/wiki/Features/FirewalldRichLanguage)

## Dependencies

No ansible role dependencies.

This role is written for a RHEL system (specifically RHEL7 and greater).

## Variables

- `firewallcmd_path` - defines the path to the firewall-cmd binary (default: `/bin/firewall-cmd`)

- `firewalld_config_path` - defines the path to the firewalld config directory (default: `/etc/firewalld`)

- `default_zone` - defines the default zone for firewalld configuration (default: `public`)

- `service_definitions` - variable that instantiates the service definition list
  - key - the name of the service (e.g. `ssh`)
    - desc - a description of the service being defined
    - proto - protocol of the service (either `tcp` or `udp`)
    - port - port number of the service

- `rule_definitions` - variable that instantiates the rule definition list
  - `family` - defines the ip rule family to use (either `ipv4` or `ipv6`)
  - `src_addr` - defines the source IP address and mask (e.g. `172.16.0.0/24`)
  - `srv_name` - OPTIONAL - defines the name of the service to use - this can be a built-in or custom service
  - `port` - OPTIONAL - if no srv_name is define, provides the port number for the rule - this can be either a single port (`22`) or range of ports (`1001-1005`)
  - `protocol` - OPTIONAL - if a port is specified,
  - `invert` - OPTIONAL - defines if matching of the source address should be inverted (e.g. if `True` then all but the source address will match)
  - `icmp_block` - OPTIONAL - defines an ICMP request type that should be rejected (e.g. `echo-request`)
  - `action` - OPTIONAL - defines the action that should result from a matching rule (either `accept`, `reject`, `drop`)

## Example Playbook

```
---

- name: uclalib_firewalld.yml
  become: yes
  become_method: sudo
  hosts: vagrant

  roles:
    - { role: uclalib_role_firewalld }

  vars:
    service_definitions:
      srv1:
        desc: "This is Service Number 1"
        proto: tcp
        port: 6789
      srv2:
        desc: "This is Service Number 2"
        proto: tcp
        port: 10112
      srv3:
        desc: "This is Service Number 3"
        proto: tcp
        port: 4756
    rule_definitions:
      - family: ipv4
        src_addr: 164.67.153.0/24
        srv_name: ssh
        action: accept
      - family: ipv4
        src_addr: 164.67.153.0/24
        port: 1001
        protocol: tcp
        action: accept
      - family: ipv4
        src_addr: 164.67.153.0/24
        port: 8001-8005
        protocol: tcp
        action: accept
      - family: ipv4
        src_addr: 164.67.153.224/32
        invert: True
        icmp_block: echo-request

```
