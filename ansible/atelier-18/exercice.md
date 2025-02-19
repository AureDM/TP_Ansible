# Exercice

- Écrivez un playbook chrony.yml qui installe un fichier de configuration personnalisé sur vos cibles. La première ligne de commentaire devra indiquer le chemin complet vers le fichier :
  * Dans certains cas ce sera /etc/chrony/chrony.conf.
  * Dans d’autres cas ce sera simplement /etc/chrony.conf.

Playbook : 
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

Fichier j2 dans le dossier template :
```
# {{ chrony_confdir }}/chrony.conf

server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

Fichier de variable ou il a toute les variables dedans :
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
```

Résultat du playbook : 
```
[vagrant@ansible playbooks]$ ansible-playbook chrony.yml

PLAY [all] **********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [debian]
ok: [rocky]
ok: [ubuntu]
ok: [suse]

TASK [Installer Chrony] *********************************************************************
changed: [debian]
changed: [ubuntu]
ok: [rocky]
changed: [suse]

TASK [Configurer Chrony] ********************************************************************
changed: [debian]
changed: [suse]
changed: [rocky]
changed: [ubuntu]

TASK [Start and enable Chrony] **************************************************************
ok: [debian]
ok: [ubuntu]
ok: [rocky]
changed: [suse]

RUNNING HANDLER [Restart Chrony] ************************************************************
changed: [debian]
changed: [ubuntu]
changed: [rocky]
changed: [suse]

PLAY RECAP **********************************************************************************
debian                     : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
