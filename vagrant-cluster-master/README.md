# Vagrant Cluster - Créer un cluster Kubernetes sur une machine Vagrant

## Attention, cette configuration ne permet pas de clusteriser plusieurs machines

### Pré-requis

- VirtualBox 6.0
- vagrant 2.26

### Mode d'emploi

1. récupérez le projet en local.

1. dans le répertoire cloné lancer la commande:

    ```bash
    vagrant up
    ```

1. La phase de provisionning installe docker puis kubernetes. Elle utilise le plugin calico pour la partie réseau.

1. À la fin de la phase de provisioning l'utilisateur root est admin du cluster.

***
