# TP 6 - Gestion des ressources

## Objectif

Cet exercice étudie comment moduler les ressources consommées par son application en fonction du niveau de sollicitation.

## Pré-requis

- Installation et configuration du client kubectl.

- Déploiement de trois pods nginx.

- Exposition du déploiement sur un Load-Balancer.

- Création d'une configmap.

- Gestion des liveness et readinessProbe.

***

1. Nous allons tout d'abord restreindre les ressources utilisées par les pod nginx, en suivant le YAML suivant:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
        name: slowmo
    spec:
        replicas: 1
        selector:
            matchLabels:
                server: slowmo
        strategy:
            type: RollingUpdate
            rollingUpdate:
                maxSurge: 2
                maxUnavailable: 1
        template:
            metadata:
                labels:
                    server: slowmo
            spec:
                containers:
                - name: slowmo
                image: raphaeldegail/slowmo:pi
                ports:
                - containerPort: 8080
                resources:
                    requests:
                        memory: "100Mi"
                        cpu: "100m"
                    limits:
                        memory: "300Mi"
                        cpu: "300m"
    ```

    Notez l'introduction du bloc resources et des sous-blocs request et limit.

    1. Mettez à jour votre déploiement et observez le nouveau pod ainsi
créé.

1. Nous allons désormais sursoliciter ce pod en utilisant un outil de charge "vegeta".

    1. Connectez-vous à la machine pour le test de charge via votre clé.

    1. Lancer un test de charge à l'aide de la commande

    ```bash
    echo "GET http://some IP/" | vegeta -cpus=8 attack -duration=20s -keepalive=false -rate=10 | tee results.bin | vegeta report
    ```

    La commande retourne à la fin du test, les latences observées, ainsi que les erreurs éventuelles. Qu'observez-vous?

    1. Essayez de lancer un test en monitorant la consommation des pods du Cluster avec la commande:

        ```bash
        kubectl top pods
        ```

1. Bien que nous ayons réduit la consommation de notre application, nous souhaiterions pouvoir augmenter les ressources disponibles devant une telle charge (si elle n'est pas malicieuse).

    1. Créer une ressource "horizontal pod autoscaler", à l'aide du fichier suivant

        ```yaml
        apiVersion: autoscaling/v1
        kind: HorizontalPodAutoscaler
        metadata:
        name: nginx-hpa
        spec:
            scaleTargetRef:
                apiVersion: apps/v1
                kind: Deployment
                name: nginx
            minReplicas: 1
            maxReplicas: 3
            targetCPUUtilizationPercentage: 50
        ```

    1. Relancez un test avec Vegeta et vérifiez que le nombre de pod augmente naturellement au cours du test. Utilisez la commande top pour vérifiez la consommation.

    1. Constatez l'amélioration des résultats des test avec vegeta, au niveau des erreurs rencontrées.

    1. Les nouveaux pods créées disparaissent-ils immédiatement après la charge?

***
