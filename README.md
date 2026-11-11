# TP - Kubernetes avec Minikube
# Mohand Said Ramy BOUSSAD
## 3. Partie 1 — Explorer l’architecture Kubernetes

**Interpétation**
* `kubectl get nodes -o wide`
> liste tout les noeuds du cluster et leur état. Ici, seul le noeud orchestrateur et pour l'instant actif et "prêt".

* `kubectl get namespaces`
> liste les differents environnements isolés du cluster

* `kubectl get pods -A`
> liste les pods à l'image d'un `docker ps`

* `kubectl get deployments -A`
> liste l'ensemble des contrôleur deployment du cluster. Pour l'instant, seul coredns est actif.

* `kubectl describe node`
> fait un rapport détaillé de l'état d'un noeud.


1. Quel est le rôle du control plane ?
> il est l'orchestarteur des noeuds "worker": verifie qu'ils sont opérationels et leur repartis les tâches à éxecuter

2. Quelle différence existe-t-il entre un nœud Kubernetes et un Pod ?
> le noeud est la machine qui fournit la puissance de calcule et hénérge un ensemble de pods qui exploite cette puissance pour executer des tâches

3. Quel contrôleur maintient le nombre de réplicas demandé par un Deployment ?
>

4. Quelle différence existe-t-il entre un conteneur et un Pod ?
> un pod peut contenir plusieurs conteneurs qui partages les mêmes propritées réseau et mémoire.

5. Quelles limites présente un cluster local à un seul nœud par rapport à un cluster de production ?
> un cluster de production est réparti sur plusieurs noeuds afin de proposer une tolérence au panne. à l'inverse, un cluster local à un seul nœud représente un SPOF (single point of failure). le monolithe se voit aussi très vite limité en terme de CPU et RAM.


---

## 4. Partie 2 — Déployer une application web
* Quel intérêt présente un namespace dans un environnement partagé entre plusieurs équipes ?
> * Isoler le travail de chaque équipe dans son environnement et de ne pas empiéter sur celui des autres.
> * repartition de la puissance de calcule sur chaque namespace. 

* Pourquoi utiliser une ConfigMap plutôt que d’intégrer directement la page dans le manifeste du Deployment ?
> séparer le mechanisme de deploiment (`Deployment`) du contenu à deployer (`ConfigMap`).


1. Quel est le rôle du Deployment par rapport au ReplicaSet ?
> `Deployment` assure la gestion du cycle de vie entier des pods, de leur création, à leur mise à jour, à leur rollback, à leur suppression. `ReplicatSet` ne fait que maintenir un nombre de pods voulu dans un état fixe actif.

2. Pourquoi ne pas accéder directement aux adresses IP des Pods ?
> En voulant accèder au même service répliqué sur des pods différents, il vaut mieux n'exposer qu'une adresse qui redirigera vers un pod "prêt". si ce pod venait à tomber, le service redirigera vers un replicat. le tout sans exposer plus d'ip que nécessaire.

3. Quelle différence existe-t-il entre `ClusterIP`, `NodePort` et `LoadBalancer` ?
> * `ClusterIP` est un service exposant une IP unique au cluster qui permet la communication entre les composants de ce dernier.
> * `NodePort` est un service exposant un port pour l'ensemble des noeuds du cluster.
> * `LoadBalancer` est un service qui acceuil le traffic externe pour l'attribuer de la manière la plus efficace aux composants du cluster.

4. Quelle différence existe-t-il entre une readiness probe et une liveness probe ?
> `readiness probe` vérifie que le pod est prêt à servir pour l'inclure (ou pas) dans les endpoints l'`endpointslice` 
>
> `liveness probe` s'assure qu'un pod exposé est opérationel ou nécessite un redémarrage.

5. Que peut-il se passer si une limite mémoire est trop basse ?
> Le service est tué et n'est plus disponible. il sera redémarré/remplacé


---

## 5. Partie 3 — Résilience et auto-rétablissement

1. Pourquoi le Pod supprimé est-il remplacé ?
> afin de satisfaire la condition `replicas: 2` de `k8s/03-deployment.yaml`.

2. Quelle différence existe-t-il entre la suppression d’un Pod et l’arrêt d’un processus dans un conteneur ?
>* à la suppression d’un pod, c'est ReplicaSet qui se charge de le remettre en route
>* à l’arrêt d’un processus dans un conteneur, c'est le kubelet du noeud qui se charge de redémarrer dans le même pod.

3. Pourquoi un redémarrage de conteneur ne signifie-t-il pas nécessairement que le cluster entier est en panne ?
> car ce conteneur est normalement repliqué pour eviter toute panne de service.

4. Que se passerait-il si le nœud unique de Minikube devenait indisponible ?
> le cluster s'effondre sur lui même. un redémarrage manuel est nécessaire

5. Pourquoi un cluster local à un seul nœud ne permet-il pas de démontrer la tolérance à la panne d’un nœud physique ?
>* car il contient l'orchestrateur qui gère cette tolèrence. s'il est HS, pas de redémarrage.
>* sans autre noeud Worker fonctionel, le Control Plane ne peut relancer les services nulle part.


---

## 6. Partie 4 — Mise à l’échelle et découverte de services

1. Quelle différence existe-t-il entre la mise à l’échelle manuelle et l’autoscaling ?
>* l’échelle manuelle maintient la replication à une valeur fixe.
>* l'autoscaling fait varier la replication selon la demande.

2. Quel est le rôle d’un Horizontal Pod Autoscaler (HPA) ?
> gérer la replication d'un pod sur le même cluster.

3. Pourquoi un HPA basé sur le CPU nécessite-t-il des métriques et des ressources correctement configurées ?
> car c'est ainsi qu'il pourra calculer l'empreinte CPU et ajuster l'autoscaling en conséquence.

4. Pourquoi quatre réplicas sur un seul nœud ne garantissent-ils pas la disponibilité en cas de panne de ce nœud ?
> car ils sont tous sur la même machine physique. pour de la haute diponibilité, il faudrait repartir les réplicas sur des noeuds différents. 


---

## 7. Partie 5 — Mise à jour et retour arrière
1. Que signifie la stratégie `RollingUpdate` ?
> les pods sont mis à jour petit à petit pour éviter tout *down time*.

2. Pourquoi une mise à jour progressive ne garantit-elle pas, à elle seule, une disponibilité parfaite ?
>* sans un nombre suffisant de replica, un endpointslice peut se retrouver sans endpoint disponible.
>* un utilisateur peut être coupé d'une connexion.
>* si des fonctionalité de l'ancienne version ne sont pas compatible avec la nouvelle, l'app peut planter pendant la transition.

3. Dans quelles conditions `rollout undo` fonctionne-t-il ?
> `rollout undo` ne fonctionne que si la modification touche le bloc `spec.template`.

4. Pourquoi faut-il versionner les configurations et planifier les migrations de base de données séparément ?
> car on veut éviter la perte des données suite à un rollback (réduire au maximum le *Recovery Point Objective*)

---

## 8. Partie 6 — Diagnostic et dépannage

### Incident A — Image introuvable

- Quel état observez-vous ?
>

- Quel événement indique la cause probable ?
>

- Comment corriger le manifeste et vérifier le retour à la normale ?
>


### Incident B — Service inaccessible

- Pourquoi le Service n’a-t-il plus de destination exploitable ?
>

- Pourquoi les Pods peuvent-ils être opérationnels alors que l’application est inaccessible via le Service ?
>

- Quelle correction permet de rétablir le routage ?
>


### Incident C — Sonde de santé incorrecte

- Quel est l’effet d’une readiness probe en échec ?
>

- En quoi son effet diffère-t-il de celui d’une liveness probe en échec ?
>

- Pourquoi une sonde mal configurée peut-elle provoquer une indisponibilité ?
>


---

## 9. Mini-projet de synthèse

- Justifier les choix d’architecture et leurs limites.

#### Analyse des limites d’un cluster local par rapport à un cluster cloud de production

---

## 12. Conclusion

> En quoi Kubernetes apporte-t-il des mécanismes d’orchestration qui dépassent la simple exécution de conteneurs avec Docker, et quelles précautions restent nécessaires pour exploiter une application de manière fiable en production ?