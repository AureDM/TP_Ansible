# Exercice

- Installez successivement les paquets tree, git et nmap sur toutes les cibles.  
Il faut utiliser le module "package" qui permet de passer au dessus des
gestionnaires de paquet différents (apt, zypper, dnf)
```

- Désinstallez successivement ces trois paquets en utilisant le paramètre supplémentaire state=absent.

- Copier le fichier /etc/fstab du Control Host vers tous les Target Hosts sous forme d’un fichier /tmp/test3.txt.

- Supprimez le fichier /tmp/test3.txt sur les Target Hosts en utilisant le module file avec le paramètre state=absent.

- Enfin, affichez l’espace utilisé par la partition principale sur tous les Target Hosts. Que remarquez-vous ?

