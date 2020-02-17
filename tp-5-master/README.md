# TP 5 - Surveiller son déploiement

## Objectif

Cet exercice étudie comment vérifier automatiquement la bonne santé de son déploiement.

## Pré-requis

- Installation et configuration du client kubectl.

- Déploiement de trois pods nginx.

- Exposition du déploiement sur un Load-Balancer.

- Création d'une configmap.

***

1. Nous allons tout d'abord simuler une erreur de déploiement liée à la configmap.

    1. Copier le fichier index.html précédent dans un fichier nommé coucou.html.

    1. Créez une nouvelle configmap à l'aide de ce fichier couccou.html.

    1. Mettez à jour votre déploiement nginx pour qu'il appelle cette nouvelle configmap et plus l'ancienne.

    1. Affichez le service sur votre navigateur et constatez l'erreur engendrée.

1. L'erreur précédente étant facilement identifiable, nous souhaitons désormais que ce soit le cluster qui fasse la vérification au lancement d'un nouveau pod.

    1. Adaptez le déploiement nginx comme suit:

        ```yaml
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
            name: nginx
        spec:
            replicas: 3
            matchLabels:
                server: nginx
            strategy:
                type: RollingUpdate
                rollingUpdate:
                    maxSurge: 2
                    maxUnavailable: 1
            template:
                metadata:
                    labels:
                        server: nginx
                spec:
                    containers:
                    - name: nginx
                        image: nginx
                    ports:
                    - containerPort: 80
                    readinessProbe:
                        httpGet:
                            path: /
                            port: 80
                        initialDelaySeconds: 3
                        periodSeconds: 5
                    volumeMounts:
                    - name: volume-for-nginx-index
                        mountPath: /usr/share/nginx/html
                    volumes:
                    - name: volume-for-nginx-index
                    configMap:
                        name: index-nginx
        ```

        Notez la présence du bloc "readinessProbe" et de la stratégie de mise à jour "rollingupdate".

    1. Mettez à jour le nouveau déploiement avec la bonne configmap et vérifiez que vous observez la bonne page web sur votre navigateur.

    1. Recréez l'erreur en mettant à jour UNIQUEMENT votre volume aec la mauvaise configmap.

    1. Affichez la page web sur votre navigateur et vérifiez que la bonne page s'affiche toujours.

    1. Insepctez les pods créés ou supprimés. Que pouvez-vous en conclure? N'oubliez pas d'utiliser la commande describe et vérifiez la colonne READY.

1. Supposons désormais que l'erreur rencontrée puisse survenir aussi pendant la vie de l'application, et pas uniquement au démarrage. Nous allons encore une fois demander au cluster de surveiller le déploiement à notre place.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
        name: nginx
    spec:
        replicas: 3
        matchLabels:
            server: nginx
        strategy:
            type: RollingUpdate
            rollingUpdate:
            maxSurge: 2
            maxUnavailable: 1
        template:
            metadata:
                labels:
                    server: nginx
            spec:
                containers:
                - name: nginx
                    image: nginx
                ports:
                - containerPort: 80
                readinessProbe:
                    httpGet:
                        path: /
                        port: 80
                    initialDelaySeconds: 3
                    periodSeconds: 5
                livenessProbe:
                    httpGet:
                        path: /
                        port: 80
                    initialDelaySeconds: 3
                    periodSeconds: 5
                volumeMounts:
                - name: volume-for-nginx-index
                    mountPath: /usr/share/nginx/html
                volumes:
                - name: volume-for-nginx-index
                configMap:
                    name: index-nginx
    ```

    Notez l'apparition du bloc "livenessProbe".

    1. Mettez à jour le déploiement en utilisant la bonne configmap.

    1. Vérifiez que la page web s'affiche correctement.

    1. Entrez dans un des pod du déploiement (commande exec) et modifier la configuration du serveur nginx pour qu'il n'aille plus chercher le fichier index.html.

    1. Relancez votre navigateur pour vérifier que vous arrivez toujours à afficher la page web.

    1. Observez les pods du déploiement et notamment celui que vous avez modifié. Que constatez-vous?

    1. Conclure sur l'utilité de la livenessProbe pour surveiller un déploiement.

***
