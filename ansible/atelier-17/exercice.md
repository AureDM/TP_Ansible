# Exercice

## Écrivez successivement deux playbooks pour mettre en place la synchronisation NTP avec Chrony sur vos quatre Target Hosts (Debian, Rocky, Suse, Ubuntu)

- Le premier playbook chrony-01.yml utilisera les modules de gestion de paquets natifs apt, dnf et zypper et s’inspirera de la méthode « gros sabots » utilisée plus haut dans cet article  

Playbook chrony-01.yml avec la méthode "gros sabots" :
```
---  # chrony-01.yml

- hosts: all

  tasks:
    - name: MAJ des paquets Debian / Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Installer Chrony sur Debian/Ubuntu
      apt:
        name: chrony
        state: present
      when: ansible_os_family == "Debian"

    - name: Installer Chrony sur Rocky
      dnf:
        name: chrony
        state: present
      when: ansible_distribution == "Rocky"

    - name: Installer Chrony sur Open SUSE
      zypper:
        name: chrony
        state: present
      when: ansible_distribution == "openSUSE Leap"

    - name: Config Chrony sur Debian/Ubuntu
      template:
        src: chrony.conf.j2
        dest: /etc/chrony/chrony.conf
      notify: Redemarrer Chrony
      when: ansible_distribution == "Debian"

    - name: Config Chrony sur Rocky
      template:
        src: chrony.conf.j2
        dest: /etc/chrony.conf
      notify: Redemarrer Chrony
      when: ansible_distribution == "Rocky"

    - name: Config Chrony sur Open SUSE
      template:
        src: chrony.conf.j2
        dest: /etc/chrony.conf
      notify: Redemarrer Chrony
      when: ansible_distribution == "openSUSE Leap"

  handlers:
    - name: Restart Chrony Debian
      service:
        name: chrony
        state: restarted
        enabled: yes
      when: ansible_os_family == "Debian"

    - name: Restart Chrony Rocky
      service:
        name: chronyd
        state: restarted
        enabled: yes
      when: ansible_distribution == "Rocky"

    - name: Restart Chrony SUSE
      service:
        name: chronyd
        state: restarted
        enabled: yes
      when: ansible_distribution == "openSUSE Leap"
...
```

Fichier de config de chrony : 
```
[vagrant@ansible playbooks]$ cat chrony.conf.j2 
# chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

Résultat du playbook : 
```
[vagrant@ansible playbooks]$ ansible-playbook chrony-01.yml

PLAY [all] **********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [debian]
ok: [ubuntu]
ok: [rocky]
ok: [suse]

TASK [MAJ des paquets Debian / Ubuntu] ******************************************************
skipping: [rocky]
skipping: [suse]
ok: [debian]
ok: [ubuntu]

TASK [Installer Chrony sur Debian/Ubuntu] ***************************************************
skipping: [rocky]
skipping: [suse]
ok: [debian]
ok: [ubuntu]

TASK [Installer Chrony sur Rocky] ***********************************************************
skipping: [debian]
skipping: [suse]
skipping: [ubuntu]
ok: [rocky]

TASK [Installer Chrony sur Open SUSE] *******************************************************
skipping: [rocky]
skipping: [debian]
skipping: [ubuntu]
ok: [suse]

TASK [Config Chrony sur Debian/Ubuntu] ******************************************************
skipping: [rocky]
skipping: [suse]
skipping: [ubuntu]
ok: [debian]

TASK [Config Chrony sur Rocky] **************************************************************
skipping: [debian]
skipping: [suse]
skipping: [ubuntu]
ok: [rocky]

TASK [Config Chrony sur Open SUSE] **********************************************************
skipping: [rocky]
skipping: [debian]
skipping: [ubuntu]
ok: [suse]

PLAY RECAP **********************************************************************************
debian                     : ok=4    changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
rocky                      : ok=3    changed=0    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
suse                       : ok=3    changed=0    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
```

- Le deuxième playbook chrony-02.yml définira trois variables chrony_package, chrony_service et chrony_confdir et utilisera le module de gestion de paquets générique package.  

On utilise le même fichier de configuration chrony que dans le playbook
différent.  

Playbook chrony-02.yml
```
---  # chrony.yml

- hosts: all
  vars_files:
    - vars/chrony_vars.yml

  tasks:
    - name: Installer Chrony
      package:
        name: "{{ chrony_package }}"
        state: present

    - name: Configurer Chrony
      template:
        src: chrony.conf.j2
        dest: "{{ chrony_confdir }}/chrony.conf"
      notify: Restart Chrony

    - name: Start and enable Chrony
      service:
        name: "{{ chrony_service }}"
        state: started
        enabled: true

  handlers:
    - name: Restart Chrony
      service:
        name: "{{ chrony_service }}"
        state: restarted
...
```

Fichier de variable à part dans un dossier vars qui est dans le dossier
playbook ou on met toutes les variables dedans : 
```
---  # vars/chrony_vars.yml

chrony_vars:
  debian:
    chrony_package: chrony
    chrony_service: chrony
    chrony_confdir: /etc

  ubuntu:
    chrony_package: chrony
    chrony_service: chrony
    chrony_confdir: /etc

  rocky:
    chrony_package: chrony
    chrony_service: chronyd
    chrony_confdir: /etc

  opensuse-leap:
    chrony_package: chrony
    chrony_service: chronyd
    chrony_confdir: /etc

chrony_package: "{{ chrony_vars[ansible_distribution|lower|replace(' ', '-')]['chrony_package'] }}"
chrony_service: "{{ chrony_vars[ansible_distribution|lower|replace(' ', '-')]['chrony_service'] }}"
chrony_confdir: "{{ chrony_vars[ansible_distribution|lower|replace(' ', '-')]['chrony_confdir'] }}"
...
```

Résultat fonctionnel : 
```
[vagrant@ansible playbooks]$ ansible-playbook chrony-02.yml

PLAY [all] **********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [debian]
ok: [ubuntu]
ok: [rocky]
ok: [suse]

TASK [Installer Chrony] *********************************************************************
ok: [suse]
ok: [debian]
ok: [ubuntu]
ok: [rocky]

TASK [Configurer Chrony] ********************************************************************
changed: [debian]
changed: [ubuntu]
ok: [suse]
ok: [rocky]

TASK [Start and enable Chrony] **************************************************************
ok: [debian]
ok: [ubuntu]
ok: [rocky]
changed: [suse]

RUNNING HANDLER [Restart Chrony] ************************************************************
changed: [debian]
changed: [ubuntu]

PLAY RECAP **********************************************************************************
debian                     : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
