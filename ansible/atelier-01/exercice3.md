#Exercice 3

Je lance une VM rocky :
```
vagrant up rocky
```

J'ai upgrade les paquets :
```
sudo dnf update
```

J'initialise l’environnement Virtualenv :
```
python3 -m venv ~/.venv/ansible
```

Je lance le venv :
```
source ~/.venv/ansible/bin/activate
```

J'update pip :
```
pip install --upgrade pip
```

J'installe Ansible : 
```
pip install ansible
```

Version Ansible :
```
(ansible) [vagrant@rocky ~]$ ansible --version
ansible [core 2.15.13]
```

Je quitte l'environnement venv : 
```
deactivate
```
