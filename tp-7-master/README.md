# TP 7 - Un exemple de ressource custom

## Objectif

Cet exercice étudie comment utiliser une ressource non-standard, avec l'exemple du Nginx ingress controller.

## Pré-requis

- Installation et configuration du client kubectl.

- Déploiement de trois pods nginx.

- Exposition du déploiement sur un Load-Balancer.

***

1. Notre cluster offre un service centralisé pouyr exposé les applications qu'il abrite: un Nginx Ingress Controller. C'est un déploiement de pods Nginx derrière un service de type Load Balancer et qui intégrer des configurations à chaud, à l'aide d'une ressource dite "Ingress". Elle se présente sous la forme:

    ```yaml
    ---
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
        annotations:
          kubernetes.io/ingress.class: nginx
        name: my-ingress
        labels:
            server: nginx
    spec:
        rules:
        - host: <some URL>
          http:
            paths:
            - path: /
              backend:
                serviceName: nginx-service
                servicePort: 80
    ```

    Notez que l'ingress se réfère à une URL et non une adresse IP car toutes les URLs se rattacheront à l'adresse IP du Nginx Ingress Controller. Le service appelé dans le fichier est bien celui de type ClusterIP.

    1. Créez la ressource ingress définie ci-dessus en donnant une URL se terminant par <.cluster.demo.wunder-it.tech>.

    1. Vérifier la bonne création de votre ressource à l'aide de la commande

        ```bash
        kubectl get ingress
        ```

    1. Tapez l'URL que vous avez donnée sur votre fichier de configuration et vérifier que vous obtenez bien votre page web.

1. À la suite de ce TP vous pouvez supprimer tous vos autres services de type Load-Balancer et constatez que vos applications sont toujours disponibles via leurs URLs.

***
