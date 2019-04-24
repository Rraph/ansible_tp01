# ansible_tp01

## Ceci est un tp réalisé en 3ème année d'informatique.
## vous retrouverez une installation automatique d'un serveur web mysql apache php ftp mettant en place un wordpress sur deux machines azure.

# 1 Les specs :

## 1.1 Les machines : 
     - une machine de contrôle de votre choix : pour ce tp une vm ubuntu A JOUR est utilisée.
     - deux machines sur lesquelles seront automatisées l'installation et la configuration des différents services.
        - DevOps1 (machine n°1) : mysql + création de la base de données
        - DevOps2 (machine n°2) : apache 2, php7.2, ftp, wordpress

## 1.2 Caractéristiques des machines :

### 1.2.1 choix de l'OS :
Les deux machines sont créées dans le cloud azure étudiant. J'ai pris pour ce tp l'offre la plus basse des machines ubuntu : Standard B1s (1 processeurs virtuels, 1 Go de mémoire).

### 1.2.2 configuration de la machine :
    - connexion grâce a une clef SSH générée sur votre machine de contrôle avec la commande 

    "$ ssh-keygen" 

    - ports ouverts : 
        - SSH (22)
        - HHTP (80)

    - disques durs : SSD Standard

# 2 Les rôles : 

Comme dit précedemment, cinq tâches sont créées réparties en deux rôles sur deux machines :  

    - machine 1 : 
        - role mysql
    - machine 2 :
        - role web 
            - apache
            - php
            - wordpress
            - ftp 

Un troisième rôle est présent, il s'agit du rôle common dirigé vers 'all' (toutes les machines). Ce rôle a pour fonction d'installer les pré-requis des services (par exemple des utilitaires pour unzip les zip). J'ai considéré que les tâches présentes dans ce roles sont des tâches basique à des machines en production.

Vous retrouverez les détails de ces rôles dans le dossier roles à la racine du projet.

# 3 Faire fonctionner le projet : 

A ce niveau, je prends en compte que vous ayez créé vos trois machines et connecté ces dernières ensemble en SSH (génération de la clef sur la machine de contrôle et indication de la clef publique sur l'interface azure.)

Une fois ceci fait, il va vous falloir connecter les machines entre elle sur ansible.

## 3.1 ansible.cfg

A la racine du projet, se trouve un fichier "ansible.cfg". Dans ce fichier, aller modifier la ligne "remote_user" en y renseignant l'user pour lequel vous avez donner votre clef SSH à la création des machines azure.

## 3.2 host_vars

Dans le dossier host_vars, vous trouverez deux fichiers yml. Ces fichiers sont les hotes du projets (les machines qui seront joint par ansible). Ils doivent avoir le nom de vos machines avec l'extension .yml.

Machine1.yml et Machine2.yml par éxemple

A l'interieur de ces fichiers, il vous faut modifier la ligne "ansible_host" en collant l'ip de vos machines créées sur ansible.

## 3.3 host.ini

Dans ce fichier, remplacez le nom des machines par le nom que vous leur avez donné. Ne mélangez pas vos machines, la machine 1 doit être dans les rôles all et mysql tandis que la machine 2 doit se trouver dans les rôles all et web.

## 3.3 testons la communication des machines entre elles.

Une fois les instructions ci-dessus faites, nous allons vérifier que nos machines peuvent bien communiquer avec notre machine de contrôle.

Pour ce faire rendez vous dans le dossier de mon projet cloné puis lancez la commande : 

```
ansible all -i host.ini -u votre_user -a "echo hello"
```

- -i : appelle notre fichier host.ini
- -u : renseigne l'user de vos machines 
- -a : execute une commande : ici un simple echo

Si tout fonctionne vous devriez avoir ce résultat : 

```
DevOps1 | SUCCESS | rc=0 >>
hello

DevOps2 | SUCCESS | rc=0 >>
hello
```

# 4 Lancement du playbook

Toujours dans le dossier, lancez la commande suivante : 

```
ansible-playbook site.yml
```

S'en suit, l'éxectuion des tâches écrites dans le playbook.

Si toute la conf précédente à bien été effectuée, vous devriez voir à la fin de l'execution du playbook le résultat suivant : 

```
PLAY RECAP *********************************************************************
DevOps1                    : ok=10   changed=0    unreachable=0    failed=0   
DevOps2                    : ok=21   changed=3    unreachable=0    failed=0   
```

## 4.1 Go sur le wordpress

Pour accéder au wordpress, ouvrez votre navigateur web favori (mozilla 4ever) en rentrez l'adresse de votre machine n°2 dans la barre de recherche : vous voilà sur votre wordpress !

Si une erreur de base de données apparaît, vous devrez créer un utilisateur mysql directement sur la machine mysql (la 1) en SSH, ca ne devrait pas être si dur que ca je vous laisse vous débrouillez pour ce point.

Enjoy !