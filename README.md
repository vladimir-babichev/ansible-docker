docker
======

Ansible role which helps to install and configure Docker CE.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```yaml
---

- name: Default usage
  hosts: all
  roles:
    - docker

- name: Configure default file (Ubuntu/Debian only)
  hosts: all
  vars:
    # Content of the default file
    docker_default_config:
      # Location of Docker binary
      DOCKERD: /usr/local/bin/dockerd
      # The daemon startup options
      DOCKER_OPTSL: --dns 8.8.8.8 --dns 8.8.4.4
    # Install Python support for Docker required for Ansible to run containers
    docker_python_pkg: python-docker
  roles:
    - docker

- name: Configure the daemon
  hosts: all
  vars:
    docker_daemon_config:
      # Enable debugging
      debug: yes
      # Configure storage driver
      storage-driver: devicemapper
      storage-opts:
        - dm.thinpooldev=/dev/mapper/thin-pool
        - dm.use_deferred_deletion=true
        - dm.use_deferred_removal=true
  roles:
    - docker
```


Role variables
--------------

Variables used by the role:

```yaml
# Package to be installed (explicit version can be specified here)
docker_pkg: docker-ce

# In order to run Docker containers via Ansible, we need to add Python package
# to support the Ansible module. On CentOS/RHEL use python-docker-py, on
# Ubuntu/Debian use python-docker.
# (null means it won't be installed)
docker_python_pkg: null

# Repo version (stable|edge|test|nightly)
docker_repo_version: stable

# YUM repo GPG key
docker_yum_repo_key: https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg

# YUM repo URL
docker_yum_repo_url: https://download.docker.com/linux/{{ ansible_distribution | lower }}/{{ ansible_distribution_major_version }}/$basearch/{{ docker_repo_version }}

# GPG key for the APT repo
docker_apt_repo_key: https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg

# APT repo string
docker_apt_repo_string: deb https://download.docker.com/linux/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} {{ docker_repo_version }}

# Name of the service
docker_service: docker

# Location of the Docker default config file
docker_default_file: /etc/default/docker

# Docker default config (see Examples in README for details)
docker_default_config: {}

# Docker conf dir path and attributes
docker_daemon_dir: /etc/docker
docker_daemon_dir_owner: root
docker_daemon_dir_group: root
docker_daemon_dir_mode: "0700"

# Location of the Docker daemon config file
docker_daemon_file: "{{ docker_daemon_dir }}/daemon.json"

# Docker daemon config (see Examples in README for details)
docker_daemon_config: {}

# Whether to install docker-compose
docker_compose_install: no

# Docker-compose version
docker_compose_version: 1.26.1

# Docker-compose URL
docker_compose_url: https://github.com/docker/compose/releases/download/{{ docker_compose_version }}/docker-compose-{{ ansible_system }}-{{ ansible_architecture }}

# Whether to install the Asible dependencies for Docker
docker_install_deps: no

# Default list of Asible dependencies for Docker
docker_deps__default:
  - "{{ 'python-docker-py' if ansible_os_family == 'RedHat' else 'python-docker' }}"

# Custom list of Asible dependencies for Docker
docker_deps__custom: []

# Final list of Asible dependencies for Docker
docker_deps: "{{
  docker_deps__default +
  docker_deps__custom }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
