zabbix_agent
=============

Ansible role which helps to install and configure Zabbix agent.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Usage
-----

```
# Default usage with no configuration changes
- name: Example 1
  hosts: machine1
  roles:
    - zabbix_agent

# Example of how to change the default settings
- name: Example 2
  hosts: machine1
  vars:
    # Change the debug level
    zabbix_agent_config_debuglevel: 4
    # Change the server IP
    zabbix_agent_config_server: 10.0.0.1
    # Change the active server IP
    zabbix_agent_config_activeserver: 10.0.0.1
  roles:
    - zabbix_agent

# Example of how to add custom settings
- name: Example 3
  hosts: machine1
  vars:
    # Define some host metadata
    zabbix_agent_config_hostmetadata:
      - aaa
      - bbb
      - ccc
    # Define the custom configuration
    zabbix_agent_config__custom:
      # Set new timeout value
      Timeout: 10
      # Produce host metadata configuration from the above variable
      HostMetadata: "{{ zabbix_agent_config_hostmetadata | join(',') }}"
  roles:
    - zabbix_agent
```


Role variables
--------------

```
# Major Zabbix version (used for Zabbix YUM repo)
zabbix_major_version: 2.4

# Zabbix YUM repo URL
zabbix_yumrepo_url: http://repo.zabbix.com/zabbix/{{ zabbix_major_version }}/rhel/{{ ansible_distribution_major_version }}/$basearch/

# Path to the zabix_server.conf file
zabbix_agent_config_file: /etc/zabbix/zabbix_agentd.conf

# Package to be installed (exact version can be specified here)
zabbix_agent_pkg: zabbix-agent

# Config params
zabbix_agent_config_pidfile: /var/run/zabbix/zabbix_agentd.pid
zabbix_agent_config_logfile: /var/log/zabbix/zabbix_agentd.log
zabbix_agent_config_logfilesize: 0
zabbix_agent_config_debuglevel: 3
zabbix_agent_config_server: 127.0.0.1
zabbix_agent_config_hostname: "{{ inventory_hostname_short }}"

# Default options
zabbix_agent_config__default:
  PidFile: "{{ zabbix_agent_config_pidfile }}"
  LogFile: "{{ zabbix_agent_config_logfile }}"
  LogFileSize: "{{ zabbix_agent_config_logfilesize }}"
  DebugLevel: "{{ zabbix_agent_config_debuglevel }}"
  Server: "{{ zabbix_agent_config_server }}"
  Hostname: "{{ zabbix_agent_config_hostname }}"

# Custom options
zabbix_agent_config__custom: {}

zabbix_agent_config__tmp: {}

# Main Zabbix web config
zabbix_agent_config: "{{
  zabbix_agent_config__tmp.update(zabbix_agent_config__default) }}{{
  zabbix_agent_config__tmp.update(zabbix_agent_config__custom) }}{{
  zabbix_agent_config__tmp }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`zabbix-server`](https://github.com/jtyr/ansible-zabbix_server) (optional)
- [`zabbix-web`](https://github.com/jtyr/ansible-zabbix_web) (optional)


License
-------

MIT


Author
------

Jiri Tyr
