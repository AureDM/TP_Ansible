# Exercice

### Un premier playbook apache-debian.yml qui installe Apache sur l’hôte debian avec une page personnalisée Apache web server running on Debian Linux.  

Playbook :
```
---  #apache-debian.yml

- hosts: debian

  tasks:

    - name: MAJ du cache APT
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Installer Apache
      apt:
        name: apache2

    - name: Démarrer et Activer Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Installer une page web customisée
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Mon premier serveur web Apache sur un Debian Linux !!! </h1>
            </body>
          </html>
...
```

Résultat du playbook : 
```
[vagrant@ansible playbooks]$ ansible-playbook apache-debian.yml

PLAY [debian] *******************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [debian]

TASK [MAJ du cache APT] *********************************************************************
changed: [debian]

TASK [Installer Apache] *********************************************************************
changed: [debian]

TASK [Démarrer et Activer Apache] ***********************************************************
ok: [debian]

TASK [Installer une page web customisée] ****************************************************
changed: [debian]

PLAY RECAP **********************************************************************************
debian                     : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Curl pour voir le résultat : 
```
[vagrant@ansible playbooks]$ curl 192.168.56.30
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>Mon premier serveur web Apache sur un Debian Linux !!! </h1>
  </body>
</html>
```

### Un deuxième playbook apache-rocky.yml qui installe Apache sur l’hôte rocky avec une page personnalisée Apache web server running on Rocky Linux.  

Playbook : 
```
---  #apache-rocky.yml

- hosts: rocky

  tasks:

    - name: Installer Apache
      dnf:
        name: httpd
        state: present

    - name: Démarrer et Activer Apache
      service:
        name: httpd
        state: started
        enabled: true

    - name: Installer une page web customisée
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Mon premier serveur web Apache sur un Rocky Linux !!! </h1>
            </body>
          </html>
...
```

Résultat du playbook :
```
[vagrant@ansible playbooks]$ ansible-playbook apache-rocky.yml

PLAY [rocky] ********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [rocky]

TASK [Installer Apache] *********************************************************************
changed: [rocky]

TASK [Démarrer et Activer Apache] ***********************************************************
changed: [rocky]

TASK [Installer une page web customisée] ****************************************************
changed: [rocky]

PLAY RECAP **********************************************************************************
rocky                      : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Curl pour voir le résultat : 
```
[vagrant@ansible playbooks]$ curl 192.168.56.20
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>Mon premier serveur web Apache sur un Rocky Linux !!! </h1>
  </body>
</html>
```

### Un troisième playbook apache-suse.yml qui installe Apache sur l’hôte suse avec une page personnalisée Apache web server running on SUSE Linux.  

Playbook : 
```
---  #apache-suse.yml

- hosts: suse

  tasks:

    - name: Installer Apache
      zypper:
        name: apache2
        state: present

    - name: Démarrer et Activer Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Installer une page web customisée
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Mon premier serveur web Apache sur un Suse Linux !!! </h1>
            </body>
          </html>
...
```

Résultat du playbook : 
```
[vagrant@ansible playbooks]$ ansible-playbook apache-suse.yml

PLAY [suse] *********************************************************************************

TASK [Gathering Facts] **********************************************************************
ok: [suse]

TASK [Installer Apache] *********************************************************************

changed: [suse]

TASK [Démarrer et Activer Apache] ***********************************************************
changed: [suse]

TASK [Installer une page web customisée] ****************************************************
changed: [suse]

PLAY RECAP **********************************************************************************
suse                       : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Curl pour voir le résultat : 
```
[vagrant@ansible playbooks]$ curl 192.168.56.40
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>Mon premier serveur web Apache sur un Suse Linux !!! </h1>
  </body>
</html>
```
