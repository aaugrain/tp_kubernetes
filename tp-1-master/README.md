# TP 1 - Configuration du client kubectl

## Objectif

Cet exercice étudie comment configurer le client kubectl.

## Pré-requis

Installation du client kubectl: <https://kubernetes.io/docs/tasks/tools/install-kubectl/>

N.B: Pour activer l'auto-complétion, lancez la commande ci-dessous

```bash
echo 'source <(kubectl completion bash)' >>~/.bashrc
```

***

1. Le client kubectl va chercher sa configuration dans le répertoire personnel de l'utilisateur qui l'utilise, dans le fichier

   ```bash
   .kube/config
   ```

   Il possède la structure suivante:

   ```yaml
   apiVersion: v1
   kind: Config
   clusters:
   contexts:
   users:
   ```

    1. Le bloc clusters contient la liste des différents clusters accessible pour le client. Chaque élément se définit ainsi:

        ```yaml
        - name: <référence du cluster dans ce fichier>
            cluster:
            certificate-authority-data: <Autorité de certification encodée en base64>
            server: <https://URL>
        ```

        Le nom indiqué sert uniquement de référence dans le fichier config et ne correspond pas à un nom défini ailleurs.

    1. Le bloc user contient la liste des utilisateurs avec lesquels le client se présente.

        ```yaml
        - name: <nom du user>
            user:
            <données d'authentification>
        ```

        Le kube-apiserver supporte plusieurs mode d'authentification, via token, via certificats, etc. Ils ne seront pas détaillés ici.

        ATTENTION! un utilisateur n'est définit qu'à l'intérieur d'un cluster et pas sur plusieurs.

    1. Le bloc contexts contient la liste des différents contextes dans lequel le client peut travailler. Chaque élément se définit ainsi:

        ```yaml
        - name: <référence du contexte pour le client>
            context:
            cluster: <référence du cluster comme défini précédemment>
            user: <référence du user comme défini précédemment>
        ```

        Un contexte est la combinaison entre un cluster, un user et un namespace. Le contexte fait appel aux noms du cluster et du user comme définis dans le fichier config.

1. Choisir un contexte.

    1. Pour afficher le fichier de config, lancez la commande:

        ```bash
        kubectl config view
        ```

    1. Pour afficher la liste des contextes:

        ```bash
        kubectl config get-contexts
        ```

    1. Enfin, pour sélectionner un contexte de travail:

        ```bash
        kubectl config use-context <nom du contexte>
        ```

1. Enfin, il est possible de créer un nouveau contexte en ligne de commande

```bash
kubectl config set-context <nom du contexte> --cluster=<nom du cluster> --user=<nom du cluster> --namespace=<nom du namespace>
```

***
