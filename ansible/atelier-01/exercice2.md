#Exercice 2 

Une fois la VM Ubuntu démarré, je configure le dépôt PPA sur la machine avec la commande :
```
sudo apt-add-repository ppa:ansible/ansible
```

Je fais les commandes de l'exercice 1 que je ne remets pas dans ce fichier,
cela prendrait trop de place pour rien :-)

J'obtiens la version d'Ansible avec "ansible --version"
```
vagrant@ubuntu:~$ ansible --version
ansible [core 2.17.8]
```

Avec la version officielle de l'exercice 1, la version avec le dépôt PPA est
bien plus récente, celle de l'exercice 1 était 2.10 ! 
