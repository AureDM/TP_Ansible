# Exercice

## Écrivez trois playbooks pour afficher des informations sur chacun des Target Hosts :

- pkg-info.yml pour afficher le gestionnaire de paquets utilisé

Le playbook est le suivant (fact a utilisé : ansible_pkg_mgr) :
```
---  # pkg-info.yml

- hosts: all

  tasks:
    - name: Afficher le gestionnaire de paquet utilisé
      debug:
        msg: "Gestionnaire de paquet utilisé : {{ansible_pkg_mgr}}"
...
```

Résultat du playbook : 
```
[vagrant@ansible playbooks]$ ansible-playbook pkg-info.yml

PLAY [all] **********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [Afficher le gestionnaire de paquet utilisé] *******************************************
ok: [rocky] => {
    "msg": "Gestionnaire de paquet utilisé : dnf"
}
ok: [debian] => {
    "msg": "Gestionnaire de paquet utilisé : apt"
}
ok: [suse] => {
    "msg": "Gestionnaire de paquet utilisé : zypper"
}

PLAY RECAP **********************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

- python-info.yml pour afficher la version de Python installée

Le playbook utilise le fact ansible_python_version :
```
---  # python-info.yml

- hosts: all

  tasks:
    - name: Afficher la version de python installé
      debug:
        msg: "La version de python installé est : {{ansible_python_version}}"

...
```

Et nous donne ce beau résultat : 
```
[vagrant@ansible playbooks]$ ansible-playbook python-info.yml

PLAY [all] **********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Afficher la version de python installé] ***********************************************
ok: [rocky] => {
    "msg": "La version de python installé est : 3.9.18"
}
ok: [debian] => {
    "msg": "La version de python installé est : 3.11.2"
}
ok: [suse] => {
    "msg": "La version de python installé est : 3.6.15"
}

PLAY RECAP **********************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

- dns-info.yml pour afficher le(s) serveur(s) DNS utilisé(s)

Ce playbook utilise le module ansible_dns.nameservers pour fonctionner :
```
---  # dns-info.yml

- hosts: all

  tasks:
    - name: Afficher le(s) serveur(s) DNS utilisé(s)
      debug:
        msg: "Serveur DNS : {{ansible_dns.nameservers}}"

...
```

Et nous donne ce résultat avec tous les serveurs DNS que chaque target possède
: 
```
[vagrant@ansible playbooks]$ ansible-playbook dns-info.yml

PLAY [all] **********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [Afficher le(s) serveur(s) DNS utilisé(s)] *********************************************
ok: [rocky] => {
    "msg": "Serveur DNS : ['10.0.2.3']"
}
ok: [debian] => {
    "msg": "Serveur DNS : ['4.2.2.1', '4.2.2.2', '208.67.220.220']"
}
ok: [suse] => {
    "msg": "Serveur DNS : ['10.0.2.3']"
}

PLAY RECAP **********************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


