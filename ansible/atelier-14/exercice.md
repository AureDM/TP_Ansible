# Exercice

### Écrivez un playbook myvars1.yml qui affiche respectivement votre voiture et votre moto préférée en utilisant le module debug et deux variables mycar et mybike définies en tant que play vars.  

```
---  # myvars1.yml


- hosts: localhost
  gather_facts: false

  vars:
    mycar: skoda
    mybike: yamaha

  tasks:
    - debug:
        msg: "Voiture: {{mycar}}, Moto : {{mybike}}"

...
```

Résultat du playbook : 
```
[vagrant@control playbooks]$ ansible-playbook myvars1.yml

PLAY [localhost] ****************************************************************************

TASK [debug] ********************************************************************************
ok: [localhost] => {
    "msg": "Voiture: skoda, Moto : yamaha"
}

PLAY RECAP **********************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### En utilisant les extra vars, remplacez successivement l’une et l’autre marque – puis les deux à la fois – avant d’exécuter le play.  

Je modifie que la 2e variable :
```
[vagrant@control playbooks]$ ansible-playbook myvars1.yml -e mybike=Ducati

PLAY [localhost] ****************************************************************************

TASK [debug] ********************************************************************************
ok: [localhost] => {
    "msg": "Voiture: skoda, Moto : Ducati"
}

PLAY RECAP **********************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Puis les 2 variables :
```
[vagrant@control playbooks]$ ansible-playbook myvars1.yml -e mycar=Audi -e mybike=Ducati

PLAY [localhost] ****************************************************************************

TASK [debug] ********************************************************************************
ok: [localhost] => {
    "msg": "Voiture: Audi, Moto : Ducati"
}

PLAY RECAP **********************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### Écrivez un playbook myvars2.yml qui fait essentiellement la même chose que myvars1.yml, mais en utilisant une tâche avec set_fact pour définir les deux variables.  

Playbook avec variable définit par set_fact :
```
---  # myvars2.yml


- hosts: localhost
  gather_facts: false

  tasks:

    - name: Définissions des variables
      set_fact:
        mycar: Toyota
        mybike: Harley

    - debug:
        msg: "Voiture: {{mycar}}, Moto : {{mybike}}"

...
```

Résultat du playbook :
```
[vagrant@control playbooks]$ ansible-playbook myvars2.yml

PLAY [localhost] ****************************************************************************

TASK [Définissions des variables] ***********************************************************
ok: [localhost]

TASK [debug] ********************************************************************************
ok: [localhost] => {
    "msg": "Voiture: Toyota, Moto : Harley"
}

PLAY RECAP **********************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### Là aussi, essayez de remplacer les deux variables en utilisant des extra vars avant l’exécution du play.  

```
[vagrant@control playbooks]$ ansible-playbook myvars2.yml -e mycar=BMW -e mybike=Honda

PLAY [localhost] ****************************************************************************

TASK [Définissions des variables] ***********************************************************
ok: [localhost]

TASK [debug] ********************************************************************************
ok: [localhost] => {
    "msg": "Voiture: BMW, Moto : Honda"
}

PLAY RECAP **********************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### Écrivez un playbook myvars3.yml qui affiche le contenu des deux variables mycar et mybike mais sans les définir. Avant d’exécuter le playbook, définissez VW et BMW comme valeurs par défaut pour mycar et mybike pour tous les hôtes, en utilisant l’endroit approprié.  

 Voici mon playbook :
 ```
 ---  # myvars3.yml


- hosts: all
  gather_facts: false

  tasks:
    - debug:
        msg: "Voiture: {{mycar}}, Moto : {{mybike}}"

...
```

Je déclare les variables dans un fichier all.yml dans un dossier group_vars à la racine du projet (:
```
---  # group_vars/all.yml

mycar: VW
mybike: BMW

...
```

Résultat du playbook :
```
[vagrant@control playbooks]$ ansible-playbook myvars3.yml

PLAY [all] **********************************************************************************

TASK [debug] ********************************************************************************
ok: [target01] => {
    "msg": "Voiture: VW, Moto : BMW"
}
ok: [target03] => {
    "msg": "Voiture: VW, Moto : BMW"
}
ok: [target02] => {
    "msg": "Voiture: VW, Moto : BMW"
}

PLAY RECAP **********************************************************************************
target01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```



### Effectuez le nécessaire pour remplacer VW et BMW par Mercedes et Honda sur l’hôte target02.  

 Je crée un dossier host_vars pour crée une variable d'hote, je nomme le
 fichier au nom de la target (target01.yml) :
 ```
 ---  # host_vars/target02.yml

mycar: Mercedes
mybike: Honda

...
```

J'obtiens bien la modification pour la target02 quand je lance mon playbook :
```
[vagrant@control playbooks]$ ansible-playbook myvars3.yml

PLAY [all] **********************************************************************************

TASK [debug] ********************************************************************************
ok: [target01] => {
    "msg": "Voiture: VW, Moto : BMW"
}
ok: [target02] => {
    "msg": "Voiture: Mercedes, Moto : Honda"
}
ok: [target03] => {
    "msg": "Voiture: VW, Moto : BMW"
}

PLAY RECAP **********************************************************************************
target01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


### Écrivez un playbook display_user.yml qui affiche un utilisateur et son mot de passe correspondant à l’aide des variables user et password. Ces deux variables devront être saisies de manière interactive pendant l’exécution du playbook. Les valeurs par défaut seront microlinux pour user et yatahongaga pour password. Le mot de passe ne devra pas s’afficher pendant la saisie.  

Voilà le playbook display_user.yml :
```
---  #display_user.yml

- hosts: localhost
  gather_facts: false

  vars_prompt:
    - name: user
      prompt: Entrer un nom d'utilisateur
      default: microlinux
      private: false

    - name: password
      prompt: Entrer un mot de passe
      default: yatahongaga
      private: true

  tasks:
    - debug:
        msg: "Le nom d'utilisateur est : {{user}} et le mot de passe : {{password}}"

...
```

Résultat du playbook ( 2 tests sont réalisés, 1 par défaut et l'autre en
rentrant un user et un password) :
```
- EXEMPLE 1 


[vagrant@control playbooks]$ ansible-playbook display_user.yml
Entrer un nom d'utilisateur [microlinux]:
Entrer un mot de passe [yatahongaga]:

PLAY [localhost] ****************************************************************************

TASK [debug] ********************************************************************************
ok: [localhost] => {
    "msg": "Le nom d'utilisateur est : microlinux et le mot de passe : yatahongaga"
}

PLAY RECAP **********************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


- EXEMPLE 2 :


[vagrant@control playbooks]$ ansible-playbook display_user.yml
Entrer un nom d'utilisateur [microlinux]: aurelien
Entrer un mot de passe [yatahongaga]:

PLAY [localhost] ****************************************************************************

TASK [debug] ********************************************************************************
ok: [localhost] => {
    "msg": "Le nom d'utilisateur est : aurelien et le mot de passe : test"
}

PLAY RECAP **********************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
