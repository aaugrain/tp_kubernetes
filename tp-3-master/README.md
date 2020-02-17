# TP 3 - Exposition d'un déploiement via un service

## Objectif

Cet exercice étudie la ressource service de Kubernetes.

## Pré-requis

- Installation et configuration du client kubectl.

- Déploiement de trois pods nginx.

***

1. Créez un service regroupant l'ensemble des pods du déploiement nginx.

   ```bash
   kubectl expose deployment/nginx --name=nginx-service --target-port=80 --port=80
   ```

    1. Observez le service ainsi créé

       ```bash
       kubectl get service
       ```

       et notez l'adresse IP du service.

    1. Observez les différents endpoints associés au service

        ```bash
        kubectl get ep nginx-service
        ```

        et vérifiez que le service se rattache bien aux trois adresses IP des pods du déploiement, avec le bon port.

    1. Affichez les adresses IP des trois pods

        ```bash
        kubectl get pod -o wide
        ```

        et vérifiez qu'aucune des trois adresses ne correspond à celle du service associé. L'adresse IP est donc bien propre au service, pas aux pods.

1. Créer un pod avec l'outil curl à l'aide du fichier suivant

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
        name: curl
    spec:
        replicas: 1
        template:
            metadata:
                labels:
                    tool: curl
            spec:
                containers:
                - name: curl
                  image: tutum/curl
                  command: ["sleep", "99999"]
    ```

    Et la commande

    ```bash
    kubectl create -f ...
    ```

    1. Introduisez-vous dans le pod curl à l'aide de la commande:

        ```bash
        kubectl exec -it <nom du pod> -- bash
        ```

    1. Une fois dans le pod curl, lancez la commande

        ```bash
        curl http://<ip du service>/
        ```

    1. Répétez l'opération plusieurs fois. Sur un autre terminal, vérifiez que les requêtes se répartissent sur les trois pods en inspectant les logs de chaque

        ```bash
        kubectl logs <nom du pod>
        ```

        Conclure sur la fonctionnalité du service.

1. Créez un nouveau service de type LoadBalancer.

   ```bash
   kubectl expose deployment/nginx --name=nginx-lb-service --target-port=80 --port=80 --type=LoadBalancer
   ```

    1. Afficher les informations du nouveau service ainsi créé. Notez notamment son adresse IP et comparez la à celle du service de la question précédente (qui était de type ClusterIP).

    1. Pouvez-vous accéder à l'adresse IP du service ainsi créé depuis votre navigateur? Aurait-ce été le cas avec le service de type ClusterIP?

    1. L'adresse IP accessible est-elle une adresse du cluster ou du Cloud qui le supporte? Comment cette adressee a-t-elle été créée? Conclure sur la notion de "Cloud Native" de Kubernetes.

***
