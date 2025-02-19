# Exercice

### Installez successivement les paquets tree, git et nmap sur toutes les cibles.  
Il faut utiliser le module "package" qui permet d'installer les commandes sur
tout type de machine avec les gestionnaires de paquet différent (apt, zypper, dnf)
```
ansible all -m package -a "name=tree,git,nmap state=present"
```

tree, git et nmap sont bien installé sur les 3 target (après avoir relancer une
2e fois la commande pour avoir un resultat plus cours à mettre dans ce rapport
d'exercice) : 
```
[vagrant@ansible ema]$ ansible all -m package -a "name=tree,git,nmap state=present" -o
debian | SUCCESS => {"cache_update_time": 1704860215,"cache_updated": false,"changed": false}
suse | SUCCESS => {"changed": false,"name": ["tree","git","nmap"],"rc": 0,"state": "present","update_cache": false}
rocky | SUCCESS => {"changed": false,"msg": "Nothing to do","rc": 0,"results": []}
```

### Désinstallez successivement ces trois paquets en utilisant le paramètre supplémentaire state=absent.  
```
ansible all -m package -a "name=tree,git,nmap state=absent"
```

Résultat de la commande fonctionnel (après 2e relance):
```
[vagrant@ansible ema]$ ansible all -m package -a "name=tree,git,nmap state=absent" -o
debian | SUCCESS => {"changed": false}
suse | SUCCESS => {"changed": false,"name": ["tree","git","nmap"],"rc": 0,"state": "absent","update_cache": false}
rocky | SUCCESS => {"changed": false,"msg": "Nothing to do","rc": 0,"results": []}
```

### Copier le fichier /etc/fstab du Control Host vers tous les Target Hosts sous forme d’un fichier /tmp/test3.txt.  
```
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
```

Résultat de la commande (après 2e relance) :
```
[vagrant@ansible ema]$ ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt" -o
debian | SUCCESS => {"changed": false,"checksum": "d39263691e31170df199aae3d93f7c556fbb3446","dest": "/tmp/test3.txt","gid": 0,"group": "root","mode": "0644","owner": "root","path": "/tmp/test3.txt","size": 743,"state": "file","uid": 0}
suse | SUCCESS => {"changed": false,"checksum": "d39263691e31170df199aae3d93f7c556fbb3446","dest": "/tmp/test3.txt","gid": 0,"group": "root","mode": "0644","owner": "root","path": "/tmp/test3.txt","size": 743,"state": "file","uid": 0}
rocky | SUCCESS => {"changed": false,"checksum": "d39263691e31170df199aae3d93f7c556fbb3446","dest": "/tmp/test3.txt","gid": 0,"group": "root","mode": "0644","owner": "root","path": "/tmp/test3.txt","secontext": "unconfined_u:object_r:user_home_t:s0","size": 743,"state": "file","uid": 0}
```

### Supprimez le fichier /tmp/test3.txt sur les Target Hosts en utilisant le module file avec le paramètre state=absent.  
```
ansible all -m file -a "path=/tmp/test3.txt state=absent"
```

Résultat de la commande (après 2e relance)
```
[vagrant@ansible ema]$ ansible all -m file -a "path=/tmp/test3.txt state=absent" -o
debian | SUCCESS => {"changed": false,"path": "/tmp/test3.txt","state": "absent"}
suse | SUCCESS => {"changed": false,"path": "/tmp/test3.txt","state": "absent"}
rocky | SUCCESS => {"changed": false,"path": "/tmp/test3.txt","state": "absent"}
```

### Enfin, affichez l’espace utilisé par la partition principale sur tous les Target Hosts. Que remarquez-vous ?  
```
ansible all -m command -a "df -h /"
```

Résultat de la commande (après 2e relance)
```
[vagrant@ansible ema]$ ansible all -m command -a "df -h /"
debian | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.3G  115G   2% /
suse | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.8G  118G   3% /
rocky | CHANGED | rc=0 >>
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/rl_rocky9-root   70G  2.4G   68G   4% /
```

On s'apercoit que même après avoir relancer la commande, le résultat de la
commande ne sort pas en "SUCCESS" en vert mais reste en orange en "CHANGED" car
effectivement les ressources utilisé par la partition ne sont jamais les mêmes
et sont variables, de très peu, mais suffisant pour ça.  
On peut noter également que la VM rocky n'utilise pas pour ces disques le
système SDA (/dev/sda3) mais plutôt MAPPER et en root (/dev/mapper/rl_rocky9-root)
