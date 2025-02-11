# Exercice

Je lance les VM : 
```
vagrant up
```

Je me connecte à la VM control :
```
vagrant ssh control
```

Vérification de l'installation d'Ansible :
```
vagrant@control:~$ type ansible
ansible is /usr/bin/ansible
```

J'essaie une 1e fois de lancer la commande des ping, rien ne marche, je dois
créer une paire de clé SSH sur la VM control et envoyer la clé publique sur
toutes les target.

Avant ça, je collecte les clés SSH publiques des Target Hosts pour les mettre
dans mon fichier known_hosts :
```
vagrant@control:~$ ssh-keyscan -t rsa target01 target02 target03 >> .ssh/known_hosts
# target02:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
# target01:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
# target03:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
```

Création de la paire de clé SSH sur control :
```
vagrant@control:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/vagrant/.ssh/id_rsa
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:Ak4aX+TXtISqOVmDOON7mBgW81EVHc75GSuQo059iLc vagrant@control
The key's randomart image is:
+---[RSA 3072]----+
|      oooo+      |
|     +  ==..     |
|  ..+.o=.+o.     |
| o+B.oB.+ . +    |
| .=o+O.=So +     |
|....B ..o .      |
|.o + o E         |
|. + .            |
|   .             |
+----[SHA256]-----+
```

J'essaie de me connecter via ssh sur les target depuis le control :
- ssh sur target01 - Fonctionne :
```
vagrant@control:~$ ssh vagrant@192.168.56.20
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-116-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Feb 11 11:04:28 AM UTC 2025

  System load:           0.0
  Usage of /:            12.7% of 30.34GB
  Memory usage:          23%
  Swap usage:            0%
  Processes:             141
  Users logged in:       0
  IPv4 address for eth0: 10.0.2.15
  IPv6 address for eth0: fd00::a00:27ff:fec8:9864


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
Last login: Tue Feb 11 10:49:43 2025 from 192.168.56.10
```

- ssh sur target02 - Fonctionne :
```
vagrant@control:~$ ssh vagrant@192.168.56.30
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-116-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Feb 11 11:05:28 AM UTC 2025

  System load:           0.08
  Usage of /:            12.7% of 30.34GB
  Memory usage:          23%
  Swap usage:            0%
  Processes:             142
  Users logged in:       0
  IPv4 address for eth0: 10.0.2.15
  IPv6 address for eth0: fd00::a00:27ff:fec8:9864


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
Last login: Tue Feb 11 10:49:30 2025 from 192.168.56.10
```
- ssh sur target03 - Fonctionne :
```
vagrant@control:~$ ssh vagrant@192.168.56.40
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-116-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Feb 11 11:05:50 AM UTC 2025

  System load:           0.08
  Usage of /:            12.6% of 30.34GB
  Memory usage:          30%
  Swap usage:            0%
  Processes:             146
  Users logged in:       0
  IPv4 address for eth0: 10.0.2.15
  IPv6 address for eth0: fd00::a00:27ff:fec8:9864


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
Last login: Tue Feb 11 10:49:07 2025 from 192.168.56.10
```

Pour se connecter automatiquement sur les target, on fait un ssh-copy-id qui
permet de distribuer la clé publique sur les target :

- ssh-copy-id sur target01 :
```
vagrant@control:~$ ssh-copy-id vagrant@192.168.56.20
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@192.168.56.20's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.56.20'"
and check to make sure that only the key(s) you wanted were added.
```

- ssh-copy-id sur target02 :
```
vagrant@control:~$ ssh-copy-id vagrant@192.168.56.30
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@192.168.56.30's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.56.30'"
and check to make sure that only the key(s) you wanted were added.
```

- ssh-copy-id sur target03 :
```
vagrant@control:~$ ssh-copy-id vagrant@192.168.56.40
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@192.168.56.40's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.56.40'"
and check to make sure that only the key(s) you wanted were added.
```

SSH depuis control vers les target fonctionnent sans mdp, ce qui veut dire que
Ansible fonctionne bien.  
En revanche, on doit configurer le nom DNS des machines dans le /etc/hosts pour
résoudre les noms DMS :
```
target01        192.168.56.20
target02        192.168.56.30
target03        192.168.56.40
```

Je lance la commande ansible qui permet de ping les machines et ça fonctionne
bien !
```
vagrant@control:~$ ansible all -i target01,target02,target03 -m ping
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
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
```




