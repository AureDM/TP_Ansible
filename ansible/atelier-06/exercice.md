# Exercice

#### Éditez /etc/hosts de manière à ce que les Target Hosts soient joignables par leur nom d’hôte simple.  
Sur la machine control, je modifie le fichier /etc/hosts pour joindre les
machines par leur nom d'hôte, j'ajoute :
```
192.168.56.20   target01
192.168.56.30   target02
192.168.56.40   target03
```

#### Configurez l’authentification par clé SSH avec les trois Target Hosts.  
Sur la machine control, je crée une paire de clé SSH
```
vagrant@control:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/vagrant/.ssh/id_rsa
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:crHLYgUNj8rwA8VSWET3AYzG5qZ7Uu1j8VHt+NLziMc vagrant@control
The key's randomart image is:
+---[RSA 3072]----+
|   O*o+..        |
|  o.*..* .       |
|  o=  o =  .     |
|   =o. . o. .    |
|   o=.. S. o     |
|  . ..o=... .    |
|   o .ooo. +     |
|  o ..+.. ..E.   |
|   o . .  .o.o.  |
+----[SHA256]-----+
```

Je fais un ssh-copy-id de la machine control vers chaque target : 
```
vagrant@control:~$ ssh-copy-id vagrant@target01
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host 'target01 (192.168.56.20)' can't be established.
ED25519 key fingerprint is SHA256:SzJYNFxdtinL9LdXn7lPx+7qpWZz38t2oKjOTAJ8I2w.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@target01's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@target01'"
and check to make sure that only the key(s) you wanted were added.
```

Si je voulais éviter d'avoir le message d'authenticity of host, j'aurais pu
faire cette commande auparavant :
```
ssh-keyscan -t rsa target01 target02 target03 >> .ssh/known_hosts
```

Je fais un test de me connecter en ssh pour voir s'il me demande bel et bien
pas de mot de passe :
```
vagrant@control:~$ ssh vagrant@target01
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-116-generic x86_64)
```

#### Installez Ansible.  

```
sudo apt update
sudo apt install ansible
```

#### Envoyez un premier ping Ansible sans configuration.  
Le ping de control vers les target fonctionne bien :
```
vagrant@control:~$ ansible all -i target01,target02,target03 -m ping
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

#### Créez un répertoire de projet ~/monprojet.

```
vagrant@control:~$ mkdir monprojet
vagrant@control:~$ ls
monprojet
vagrant@control:~$ pwd
/home/vagrant
```

#### Créez un fichier vide ansible.cfg dans ce répertoire.

```
vagrant@control:~/monprojet$ touch ansible.cfg
vagrant@control:~/monprojet$ ls
ansible.cfg
```

#### Vérifiez si ce fichier est bien pris en compte par Ansible.  

Le fichier ansible.cfg est bien pris en compte par Ansible :
```
vagrant@control:~/monprojet$ ansible --version
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0]
```

#### Spécifiez un inventaire nommé hosts.  
Création du fichier hosts vide :
```
vagrant@control:~/monprojet$ touch hosts
vagrant@control:~/monprojet$ ls
ansible.cfg  hosts
```

#### Activez la journalisation dans ~/journal/ansible.log.  
J'ajoute ce morceaux de code dans le fichier ~/monprojet/ansible.cfg :
```
[defaults]
log_path = ~/journal/ansible.log
```

Je crée le dossier journal :
```
mkdir ~/journal
```

#### Testez la journalisation.  
Pour tester la journalisation, je fais une commande ansible, de ping les target par exemple, le résultat ira
dans le fichier de log crée juste avant :
```
vagrant@control:~/monprojet$ ansible all -i target01,target02,target03 -m ping -o
target01 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
target03 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
target02 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
```

Résultat dans le fichier de log :
```
vagrant@control:~/monprojet$ cat ~/journal/ansible.log
2025-02-12 11:03:20,177 p=4083 u=vagrant n=ansible | target01 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
2025-02-12 11:03:20,186 p=4083 u=vagrant n=ansible | target03 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
2025-02-12 11:03:20,202 p=4083 u=vagrant n=ansible | target02 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
```

#### Créez un groupe [testlab] avec vos trois Target Hosts.  
J'ouvre le fichier monprojet/hosts et j'ajoute les lignes :
```
[testlab]
target01
target02
target03
```


#### Définissez explicitement l’utilisateur vagrant pour la connexion à vos cibles.  
J'ajoute dans le fichier monprojet/hosts :
```
[testlab:vars]
ansible_user=vagrant
```

#### Envoyez un ping Ansible vers le groupe de machines [all].

J'obtiens une erreur comme quoi il ne trouve pas de fichier d'inventaire : 
```
vagrant@control:~/monprojet$ ansible all -m ping -o
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit
localhost does not match 'all'
```

Normal ! Je dois spécifier le chemin de l'inventaire dans le fichier
ansible.cfg :
```
[defaults]
inventory = ./hosts
```

Effectivement, ça fonctionne mieux comme ça : 
```
vagrant@control:~/monprojet$ ansible all -m ping -o
target02 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
target03 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
target01 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
```

#### Définissez l’élévation des droits pour l’utilisateur vagrant sur les Target Hosts.  
J'ajoute dans le fichier d'inventaire (hosts) ansible_become=yes permettant
l'élévation des droits :
```
[testlab:vars]
ansible_user=vagrant
ansible_become=yes
```

#### Affichez la première ligne du fichier /etc/shadow sur tous les Target Hosts.

```
vagrant@control:~/monprojet$ ansible all -m command -a "head -n 1 /etc/shadow"
target01 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
```

#### Quittez le Control Host et supprimez toutes les VM de l’atelier.  
- Quitter le Control Host :
```
vagrant@control:~/monprojet$ exit
logout
Connection to 127.0.0.1 closed.
```

- Supprimez les VM :
```
[ema@localhost:atelier-06] $ vagrant destroy -f
==> target03: Forcing shutdown of VM...
==> target03: Destroying VM and associated drives...
==> target02: Forcing shutdown of VM...
==> target02: Destroying VM and associated drives...
==> target01: Forcing shutdown of VM...
==> target01: Destroying VM and associated drives...
==> control: Forcing shutdown of VM...
==> control: Destroying VM and associated drives...
```
