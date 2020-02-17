# TP 2 - Mon premier pod

## Objectif

Cet exercice étudie la ressource de type pod.

## Pré-requis

Installation et configuration du client kubectl.

***

1. Créez un déploiement à l'aide de la commande

   ```bash
    kubectl create deployment nginx --image=nginx:1.16
    ```

    1. Observez le pod créé

       ```bash
       kubectl get pod -o yaml
       ```

       et cherchez dans le métadata, la section "ownerReferences". Celle-ci fait référence à un replicaset du nom "nginx-HASH".

    1. Observez le replicaset en question

        ```bash
        kubectl get rs <nom du replicaset> -o yaml
        ```

        et cherchez à nouveau la section "ownerReferences" dans les méta-données. Celle-ci fait référence cette fois au déploiement "nginx".

    1. Enfin insepctez le déploiement créé

        ```bash
        kubectl get deployment nginx -o yaml
        ```

        et notez l'absence du bloc "ownerReferences". Conclure sur la hiérarchie entre le déploiement, le replicaset et le pod. Pouvez-vous retrouver cette hiérarchie par les noms du pod, replicaset, déploiement?

1. Augmenter le nombre de replicas du déploiement "nginx"

    ```bash
    kubectl scale --replicas=3 deployment/nginx
    ```

    1. Observez le nombre de pods alors présent. De même afficher le replicaset et le déploiement.

    1. Que pouvez-vous dire du nom des deux nouveaux pods ainsi créés?

1. Nous allons réaliser une montée de version du déploiement

    ```bash
    kubectl set image deployment/nginx nginx=nginx:1.17
    ```

    1. Observez la disparition des pods de l'ancienne version et l'apparition de ceux de la nouvelle version. La transition est-elle brutale?

    1. Afficher tous les replicasets et vérifier qu'il en existe bien deux désormais, un par version de l'image déployée.

    1. Combien y a-t-il de déploiement nginx? Conclure sur la manière dont un déploiement fait évoluer la version des pods. Vérifiez votre hypothèse en utilisant la commande

        ```bash
        kubectl describe deployment nginx
        ```

***
