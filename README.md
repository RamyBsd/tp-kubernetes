# TP - Kubernetes avec Minikube
## 3. Partie 1 — Explorer l’architecture Kubernetes

**Interpétation**
* `kubectl get nodes -o wide`
>

* `kubectl get namespaces`
>

* `kubectl get pods -A`
>

* `kubectl get deployments -A`
>

* `kubectl describe node`
>


1. Quel est le rôle du control plane ?
>

2. Quelle différence existe-t-il entre un nœud Kubernetes et un Pod ?
>

3. Quel contrôleur maintient le nombre de réplicas demandé par un Deployment ?
>

4. Quelle différence existe-t-il entre un conteneur et un Pod ?
>

5. Quelles limites présente un cluster local à un seul nœud par rapport à un cluster de production ?
>


---

## 4. Partie 2 — Déployer une application web
* Quel intérêt présente un namespace dans un environnement partagé entre plusieurs équipes ?
>

* Pourquoi utiliser une ConfigMap plutôt que d’intégrer directement la page dans le manifeste du Deployment ?
>


1. Quel est le rôle du Deployment par rapport au ReplicaSet ?
>

2. Pourquoi ne pas accéder directement aux adresses IP des Pods ?
>

3. Quelle différence existe-t-il entre `ClusterIP`, `NodePort` et `LoadBalancer` ?
>

4. Quelle différence existe-t-il entre une readiness probe et une liveness probe ?
>

5. Que peut-il se passer si une limite mémoire est trop basse ?
>


---

## 5. Partie 3 — Résilience et auto-rétablissement

1. Pourquoi le Pod supprimé est-il remplacé ?
>

2. Quelle différence existe-t-il entre la suppression d’un Pod et l’arrêt d’un processus dans un conteneur ?
>

3. Pourquoi un redémarrage de conteneur ne signifie-t-il pas nécessairement que le cluster entier est en panne ?
>

4. Que se passerait-il si le nœud unique de Minikube devenait indisponible ?
>

5. Pourquoi un cluster local à un seul nœud ne permet-il pas de démontrer la tolérance à la panne d’un nœud physique ?
>


---

## 6. Partie 4 — Mise à l’échelle et découverte de services

1. Quelle différence existe-t-il entre la mise à l’échelle manuelle et l’autoscaling ?
>

2. Quel est le rôle d’un Horizontal Pod Autoscaler (HPA) ?
>

3. Pourquoi un HPA basé sur le CPU nécessite-t-il des métriques et des ressources correctement configurées ?
>

4. Pourquoi quatre réplicas sur un seul nœud ne garantissent-ils pas la disponibilité en cas de panne de ce nœud ?
>


---

## 7. Partie 5 — Mise à jour et retour arrière
1. Que signifie la stratégie `RollingUpdate` ?
>

2. Pourquoi une mise à jour progressive ne garantit-elle pas, à elle seule, une disponibilité parfaite ?
>

3. Dans quelles conditions `rollout undo` fonctionne-t-il ?
>

4. Pourquoi faut-il versionner les configurations et planifier les migrations de base de données séparément ?
>


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