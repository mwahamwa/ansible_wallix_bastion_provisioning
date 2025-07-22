# Playbook de Provisionnement du Bastion Wallix 🚀

Ce playbook Ansible permet de provisionner un Bastion Wallix en créant des utilisateurs et des appareils. Il utilise des rôles Ansible pour organiser les tâches et des variables pour configurer les paramètres.

## Table des Matières

1. [Prérequis](#prérequis)
2. [Structure du Projet](#structure-du-projet)
3. [Configuration](#configuration)
4. [Utilisation](#utilisation)
5. [Exemples](#exemples)
6. [Licence](#licence)

## Prérequis

- [Ansible](https://www.ansible.com/) installé sur votre machine.
- Accès à un Bastion Wallix.
- Informations d'authentification pour l'API du Bastion Wallix.

## Structure du Projet

```plaintext
.
├── playbook.yml
└── roles
    └── bastion_provisioning
        ├── tasks
        │   ├── main.yml
        │   └── auth.yml
        ├── vars
        │   └── main.yml
        └── defaults
            └── main.yml
```

| Fichier | Description |
|---------|-------------|
| `playbook.yml` | Le playbook principal qui inclut le rôle de provisionnement du Bastion. |
| `roles/bastion_provisioning/tasks/main.yml` | Fichier principal des tâches du rôle. |
| `roles/bastion_provisioning/tasks/auth.yml` | Fichier de tâches pour l'authentification. |
| `roles/bastion_provisioning/vars/main.yml` | Fichier de variables pour le rôle. |
| `roles/bastion_provisioning/defaults/main.yml` | Fichier de variables par défaut pour le rôle. |

## Configuration

### Fichier `roles/bastion_provisioning/vars/main.yml`

Le fichier `roles/bastion_provisioning/vars/main.yml` contient les variables de configuration pour le rôle.

| Variable | Description | Exemple |
|----------|-------------|---------|
| `bastion_url` | URL de l'API du Bastion Wallix | `"https://mybastion.lab/api"` |
| `api_user` | Nom d'utilisateur pour l'authentification à l'API | `"username"` |
| `api_password` | Mot de passe pour l'authentification à l'API | `"usernamepass"` |
| `users` | Liste des utilisateurs à créer dans le Bastion | Voir tableau ci-dessous |
| `devices` | Liste des appareils à créer dans le Bastion | Voir tableau ci-dessous |

Exemple de contenu pour `roles/bastion_provisioning/vars/main.yml` :

```yaml
---
bastion_url: "https://mybastion.lab/api"
api_user: "username"
api_password: "usernamepass"
users:
  - user_name: "toto"
    user_email: "toto@toto.com"
    user_password: "MyH0lyPass1"
  - user_name: "tito"
    user_email: "tito@toto.com"
    user_password: "MyH0lyPass2"
devices:
  - device_name: "debian"
    device_host: "10.11.12.13"
  - device_name: "mailserver"
    device_host: "10.11.12.14"
```

### Fichier `playbook.yml`

Le fichier `playbook.yml` est le playbook principal qui inclut le rôle de provisionnement du Bastion.

| Section | Description | Exemple |
|---------|-------------|---------|
| `hosts` | Spécifie les hôtes sur lesquels le playbook sera exécuté | `localhost` |
| `gather_facts` | Désactive la collecte des faits Ansible | `false` |
| `vars` | Définit les variables spécifiques au playbook | Voir tableau ci-dessous |
| `roles` | Inclut le rôle `bastion_provisioning` et applique les tags spécifiés | Voir tableau ci-dessous |

Exemple de contenu pour `playbook.yml` :

```yaml
---
- name: Playbook to create assets and users in Bastion
  hosts: localhost
  gather_facts: false
  vars:
    bastion_tags:
      - users
      - devices
  roles:
    - role: bastion_provisioning
      tags: "{{ item }}"
      loop: "{{ bastion_tags }}"
```

## Utilisation

### Exécution du Playbook

Pour exécuter le playbook avec les variables par défaut, utilisez la commande suivante :

```sh
ansible-playbook playbook.yml
```

⚠️ **Attention** : Sans spécifier de tags, le playbook ne fera rien. Vous devez spécifier les tags appropriés pour exécuter les tâches souhaitées.

### Tags Disponibles

| Tag | Description |
|-----|-------------|
| `users` | Exécute les tâches pour créer des utilisateurs dans le Bastion. |
| `devices` | Exécute les tâches pour créer des appareils dans le Bastion. |

### Surcharge des Variables

Pour surcharger les variables par défaut, vous pouvez utiliser l'option `-e` pour spécifier les variables en ligne de commande. Voici quelques exemples :

#### Surcharger une variable simple

Pour changer l'URL du Bastion, utilisez :

```sh
ansible-playbook playbook.yml -e "bastion_url=https://newbastion.lab/api" --tags "users,devices"
```

#### Surcharger une liste de variables

Pour ajouter un nouvel utilisateur, utilisez :

```sh
ansible-playbook playbook.yml -e "users=[{'user_name': 'newuser', 'user_email': 'newuser@toto.com', 'user_password': 'NewPass1'}]" --tags "users"
```

#### Surcharger plusieurs variables

Pour changer l'URL du Bastion et ajouter un nouvel utilisateur, utilisez :

```sh
ansible-playbook playbook.yml -e "bastion_url=https://newbastion.lab/api users=[{'user_name': 'newuser', 'user_email': 'newuser@toto.com', 'user_password': 'NewPass1'}]" --tags "users"
```

### Utilisation des Tags

Pour exécuter uniquement les tâches avec un tag spécifique, utilisez l'option `--tags`. Par exemple, pour exécuter uniquement les tâches avec le tag `users`, utilisez :

```sh
ansible-playbook playbook.yml --tags "users"
```

Pour exécuter les tâches avec plusieurs tags, utilisez :

```sh
ansible-playbook playbook.yml --tags "users,devices"
```

## Exemples

### Exemple 1 : Exécution avec les Variables par Défaut et Tags

```sh
ansible-playbook playbook.yml --tags "users,devices"
```

### Exemple 2 : Surcharge des Variables et Tags

```sh
ansible-playbook playbook.yml -e "bastion_url=https://newbastion.lab/api users=[{'user_name': 'newuser', 'user_email': 'newuser@toto.com', 'user_password': 'NewPass1'}]" --tags "users"
```

### Exemple 3 : Utilisation des Tags

```sh
ansible-playbook playbook.yml --tags "users"
```

```sh
ansible-playbook playbook.yml --tags "users,devices"
```