# Exercice 1

- Démarrez la VM ubuntu depuis le répertoire atelier-01.
```
vagrant up ubuntu
```

- Connectez-vous à cette VM.
```
vagrant ssh ubuntu
```

- Rafraîchissez les informations sur les paquets.

Une fois connecté sur la VM ubuntu :
```
sudo apt update
```

- Recherchez le paquet ansible avec les options qui vont bien.
```
apt-cache search ansible --names-only ansible
```

- Installez le paquet officiel fourni par la distribution.
```
sudo apt install ansible
```

- Vérifiez si l’installation s’est bien déroulée.
```
ansible --version
```

- Notez la version d’Ansible.
```
vagrant@ubuntu:~$ ansible --version
ansible 2.10.8
```

- Déconnectez-vous et supprimez la VM.

Déconnexion de la VM :
```
exit
```

Suppression de la VM :
```
vagrant destroy -f ubuntu
```

