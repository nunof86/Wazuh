# Playbook - Ansible

## Ansible Playbook

1. Replace the <mark style="color:red;">`hosts:`</mark> option to <mark style="color:red;">**localhost**</mark> or other <mark style="color:red;">**host**</mark> of your choice.
2. With this playbook the <mark style="color:red;">**credentials**</mark> and the <mark style="color:red;">**dashboard port**</mark> remains the default.

```yaml
- name: Install Wazuh Docker
  hosts: wazuh_server
  become: true
  any_errors_fatal: true
  max_fail_percentage: 0

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: System update
      apt:
        update_cache: yes
      changed_when: false
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Dist upgrade
      apt:
        upgrade: dist
      register: upgrade_output
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Install required packages
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - git
          - software-properties-common
        state: present
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Add Docker APT key
      apt_key:
        url: https://download.docker.com/linux/{{ ansible_distribution|lower }}/gpg
        state: present
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Add Docker APT repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/{{ ansible_distribution|lower }} {{ ansible_lsb.codename }} stable
        state: present
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Install Docker
      apt:
        name: docker-ce
        state: latest
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Download and Install Docker Compose
      get_url:
        url: https://github.com/docker/compose/releases/latest/download/docker-compose-Linux-x86_64
        dest: /usr/bin/docker-compose
        mode: '0755'
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Clone Wazuh Docker Repository
      git:
        repo: https://github.com/wazuh/wazuh-docker.git
        dest: /opt/wazuh-docker
        version: v4.4.5

    - name: Generate Indexer Certificates
      command: docker-compose -f generate-indexer-certs.yml run --rm generator
      args:
        chdir: /opt/wazuh-docker/single-node

    - name: Start Wazuh Docker Containers
      command: docker-compose -f docker-compose.yml up -d
      args:
        chdir: /opt/wazuh-docker/single-node
```
