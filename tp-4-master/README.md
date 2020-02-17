# TP 4 - Améliorer son déploiement

## Objectif

Cet exercice étudie quelques configurations supplémentaire pour optimiser son déploiement.

## Pré-requis

- Installation et configuration du client kubectl.

- Déploiement de trois pods nginx.

- Exposition du déploiement sur un Load-Balancer.

***

1. Nous allons essayer de modifier la page d'accueil du pod nginx.

    1. Créer un fichier "index.html" sur votre machine local en laissant libre cours à votre imagination. Vous pouvez vous inspirer du squelette suivant:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Le titre de la page qui s'affichera dans l'onglet de votre navigateur</title>
    </head>

    <body>
        <h1>Le titre qui apparaîtera sur la page elle-même.</h1>
        <p>Un paragraphe</p>
    </body>

    </html>
    ```

    1. Créez une configmap à l'aide de ce fichier

    ```bash
    kubectl create configmap index-nginx --from-file=index.html
    ```

    et affichez le contenu de cette configmap en format YAML.

    1. Nous allons adapter le déploiement nginx pour qu'il prenne en compte le nouveau fichier index.html via un volume. Adaptez le YAML de votre déploiement avec la structure suivante:

        ```yaml
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
            name: nginx
        spec:
            replicas: 1
            selector:
                matchLabels:
                    server: nginx
            template:
                metadata:
                    labels:
                        server: nginx
                spec:
                    containers:
                    - name: nginx-fail
                      image: nginx
                      ports:
                      - containerPort: 80
                      volumeMounts:
                      - name: volume-for-nginx-index
                        mountPath: /usr/share/nginx/html
                    volumes:
                    - name: volume-for-nginx-index
                      configMap:
                        name: index-nginx
        ```

        1. Mettez à jour le déploiement nginx avec ce nouveau YAML.

        1. Vérifiez que la mise à jour s'est correctement déroulée et affichez à nouveau votre service sur votre navigateur. Vérifiez que la nouvelle page s'affiche correctement.

        1. Augmentez le nombre de replica du déploiement à trois. Affichez à nouveau la page web sur votre navigateur et vérifiez que vous obtenez toujours la nouvelle page.

        1. Inspectez les logs des trois pods et assurez-vous que même les requêtes servies par les nouveaux pods renvoient la nouvelle page.

        1. Conclure sur la notion de configuration commune via les configmap et les volumes.

***
