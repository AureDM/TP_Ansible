# Exercice

- Écrivez un playbook kernel.yml qui affiche les infos détaillées du noyau sur tous vos Target Hosts. Utilisez la commande uname -a et le module debug avec le paramètre msg.  

Playbook :
```
---  # kernel.yml

- hosts: all
  gather_facts: false

  tasks:
    - name: Information détaillé du noyau
      command: uname -a
      changed_when: false
      register: kernel_info


    - debug:
        msg: "{{kernel_info.stdout_lines}}"


...
```

Résultat du playbook : 
```
[vagrant@ansible playbooks]$ ansible-playbook kernel.yml

PLAY [all] **********************************************************************************

TASK [Information détaillé du noyau] ********************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [debug] ********************************************************************************
ok: [rocky] => {
    "msg": [
        "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
    ]
}
ok: [debian] => {
    "msg": [
        "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
    ]
}
ok: [suse] => {
    "msg": [
        "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
    ]
}

PLAY RECAP **********************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0
skipped=0    rescued=0    ignored=0
```


- Essayez d’obtenir le même résultat en utilisant le paramètre var du module debug.

J'obtiens exactement le même résultat avec ce playbook :
```
---  # kernel.yml

- hosts: all
  gather_facts: false

  tasks:
    - name: Information détaillé du noyau
      command: uname -a
      changed_when: false
      register: kernel_info


    - debug:
        var: kernel_info.stdout_lines


...
```


- Écrivez un playbook packages.yml qui affiche le nombre total de paquets RPM installés sur les hôtes rocky et suse (rpm -qa | wc -l).  

Playbook : 
```
---  # packages.yml

- hosts: rocky, suse
  gather_facts: false


  tasks:
    - name: Nombre total de paquets RPM installé
      shell: rpm -qa | wc -l
      changed_when: false
      register: ttl_packages


    - debug:
        msg: "Nombre total de paquets RPM installés : {{ttl_packages.stdout}}"
...
```

Le playbook est bien fonctionnel (il faut utiliser le module shell pour que ça
fonctionne) : 
```
[vagrant@ansible playbooks]$ ansible-playbook packages.yml 

PLAY [rocky, suse] **************************************************************************

TASK [Nombre total de paquets RPM installé] *************************************************
ok: [rocky]
ok: [suse]

TASK [debug] ********************************************************************************
ok: [rocky] => {
    "msg": "Nombre total de paquets RPM installés : 671"
}
ok: [suse] => {
    "msg": "Nombre total de paquets RPM installés : 917"
}

PLAY RECAP **********************************************************************************
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

