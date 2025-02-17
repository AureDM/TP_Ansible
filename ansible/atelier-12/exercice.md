# Exercice

Consigne : 
```
Écrivez un playbook chrony.yml qui assure la synchronisation NTP de tous vos Target Hosts :

- Installez le paquet chrony.
- Activez et démarrez le service chronyd correspondant.
- Installez une configuration personnalisée (cf. ci-dessous).
- Prenez en compte cette nouvelle configuration.
```

Playbook : 
```
--- # chrony.yml

- hosts: redhat

  tasks:
    - name: Installer le paquet chrony
      dnf:
        name: chrony
        state: present

    - name: Activer et démarrer le service chronyd
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Installer une configuration personnalisée
      copy:
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: 0644
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          # server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Prendre en compte la nouvelle config

  handlers:

    - name: Prendre en compte la nouvelle config
      service:
        name: chronyd
        state: restarted
...
```

Résultat (à la 2e relance) :
```
[vagrant@control playbooks]$ ansible-playbook chrony.yml 

PLAY [redhat] *******************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [target01]
ok: [target03]
ok: [target02]

TASK [Installer le paquet chrony] ***********************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Activer et démarrer le service chronyd] ***********************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Installer une configuration personnalisée] ********************************************
ok: [target03]
ok: [target02]
ok: [target01]

PLAY RECAP **********************************************************************************
target01                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


idem potence = si pas de changement alors résultat des playbook en SUCCESS.
Les handlers permettent de faire la task seulement si il y a eu un changement
sur la tasks précédentes.
