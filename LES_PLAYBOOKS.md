# Les Playbooks

## Ad-Hoc commande pour afficher les facts 
```ansible target2 -i inventory_children -m setup```

## Les facts dans un fichier YAML
### Utilisation de when 
```ansible-playbook -i inventory_children ansible_facts_using_when.yml```
### Utilisation de la commande assert 
```ansible-playbook -i inventory_children ansible_facts_using_assert.yml```  

### Le prompt et les conditions
```ansible-playbook -i inventory_children conditions.yml --limit centos```

### les boucles
```ansible-playbook -i inventory_children loops.yml --limit centos```

## Passage d'information entre les hosts
### Runtime Inventory 
Pour passer des variables entre remote-to-remote host il est possible
de creer un host de type dummy et lui attacher des variables pour les passer 
vers l'autre host.

```ansible-playbook -i inventory_children runtime_inventory_additions.yml```

### Inventaire dynamique
Faire un fork de ce repo  
```https://github.com/numerica-ansible/ansible-dynamic-repository.git```
dans votre repo github
et faire un git clone, dans votre home directory,  la vm ansible controller   
Changer le fichier my_inventory.py   
mettre l'adresse IP de votre remote VM dans la structure JSON   
et  tapez
```ansible-playbok -i my_inventory.py playbook.yml```


## Utilisation des variables et des filtres 

### Changer the Message Of The Day (MOTD) 
```ansible-playbook -i inventory_children motd.yml --limit target2```

### Les filters, creer son propre filtre 
```ansible-playbook -i inventory_children new_filter.yml --limit target2```

## Les modules
### Creer son propre module 
Allez dans votre repository github pour creer un token   
clicker sur les Settings de votre compte github et selectionnez  
Developer Settings et ensuite Personnal Access Tokens 
Creer un token et lui donner les droits pour creer un repo github.  
Dans votre home directory faire un ```vi token``` et copier votre
token.  
Toujours sous le prompt venv
faire ```pip3 install requests``` et 
```ansible-playbook -i inventory_children ansible_create_module.yml```
## Les Roles

### Mettre le precedement playbook dans un role 
Dans votre home directory toujours sous le prompt venv
faire ```mkdir example-role```  
et ```cd example-role```  
```ansible-galaxy init github.role```  
creer un ficher playbook.yml    
```yaml
- name: use a dedicated Ansible module
  hosts: localhost
  roles:
    - { role: github.role }
```
Dans example-role/github.role/tasks/main.yml 
copier le code suivant
```yaml
# tasks file for github.role
- name: Create a github Repo
  github_repo:
    github_auth_key: "{{ git_key }}"
    name: "repo-create-with-ansible"
    description: "Ansible module for github"
    private: no
    has_issues: no
    has_wiki: no
    has_downloads: no
    state: present
  register: result
```
Dans  example-role/github.role/defaults/main.yml
```yaml
# defaults file for github.role
git_key: d6f90b4be8axxxxxxxxxxxxxxx
```
Revenez dans votre directory ou est installe le playbook et faire
``` ansible-playbook -i ../inventory_children playbook.yml```

## Ansible Vault
Nous allons voir comment crypter nos informations sensibles avec Ansible
Crypter la variable token dans votre projet @ defaults/main.yml  
Tapez  
```ansible-vault encrypt  main.yml```   
entrez votre mot de passe   
mettrez ce mot de passe dans un fichier  
```vi /home/<home_directory>/mysecret```   
Vous pouvez executer le playbook avec   
```ansible-playbook -i ../inventory_children --vault-password-file /home/<home_directory>/mysecret playbook.yml``` 
vous pouvez metter le path de ce fichier dans votre ```.bash_profile``` file.  
```export  ANSIBLE_VAULT_PASSWORD_FILE=/home/<home>/mysecret```      
et vous entrez la commande sans vous soucier du fichier du mot de passe  
```ansible-playbook -i ../inventory_children playbook.yml``` 

















































