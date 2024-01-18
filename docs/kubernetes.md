---
title: 2 - Kubernetes
description: Partie du rapport traitant de Kubernetes
---

## Introduction
	
Kubernetes est une plate-forme permettant d'automatiser le déploiement, la montée en charge et la mise en œuvre de conteneurs d'application sur des grappes de serveurs.

Elle favorise à la fois l'écriture de configuration et l'automatisation. Les services, le support et les outils Kubernetes sont largement disponibles.<br>
Kubernetes est un système open source. Il est développé par Google et la Cloud Native Computing Foundation. La première annonce de Kubernetes a été faite en 2014 et sa  première version est sortie en 2015.

Il permet le stockage des informations sensibles comme des mots de passe, des jetons d’authentification ou des clés. 

Il ne faut pas confondre l’utilisation de kubernetes avec :

- un service mesh : un service d’application préconfiguré qui permet aux différents services de communiquer entre eux, de partager des données et d’assurer une cohérence tout au long du cycle de vie d’une application. Par exemple, le service mesh Istio est un outil pour Kubernetes.
- Platform as a Service system tel que Docker.

Il existe plusieurs distributions telles que minikube pour la gestion d’un cluster local ou Amazon Elastic Kubernetes Service (Amazon EKS).<br>
Kubernetes est basé sur le langage de programmation Go. Il possède une communauté active sur la détection et le patch de failles.

## Fonctionnement de Kubernetes

### Architecture

Les pods sont les plus petites unités informatiques déployables qui peuvent être créées et gérées dans Kubernetes. Un pod est un groupement d’un ou plusieurs conteneurs qui partagent les mêmes ressources et stockage. Le conteneur d’un pod peut être exécuté en mode privilégié.

Une node est une machine physique ou virtuelle (en fonction du cluster). Tous les clusters possèdent au moins une node. Chaque node est managée par le panneau de contrôle et contient les services nécessaires pour exécuter chaque pod.

Les composants d’une node incluent :

- kubelet est un agent qui permet d'exécuter chaque node et permet de vérifier que chaque conteneur est dans un pod.
- Container Runtimes est un logiciel qui permet de faire tourner les pods, il est responsable pour les conteneurs en cours d'exécution.

Le Container Runtime Interface (CRI) permet à kubelet d’utiliser les conteneurs sans avoir besoin de recompiler tous les composants d’un cluster.

Kubernetes possède un ordonnanceur pour s’assurer du bon fonctionnement des pods avec les nodes. Ce fonctionnement dépend de contraintes comme les ressources ou les politiques.

Le Cloud Controller Manager permet à Kubernetes de contrôler les ressources. Il possède de nombreux outils comme le node controller qui est responsable de la mise à jour des nodes afin qu’elles correspondent aux informations du serveur. Il permet également de configurer des routes dans le cloud ainsi que du filtrage de paquets, de l’adresse IP et du load balancing.

Les applications peuvent accéder aux ressources d’un hôte sans avoir nécessairement besoin des informations contenues dans celui-ci. 

L’utilisation de baux permet d’avoir un mécanisme qui bloque les ressources partagées et coordonne les activités entre les différents membres d’un même ensemble.

Kubernetes a un daemon (kube-controller-manager) qui est composé de plusieurs contrôleurs :

- Node controller qui permet de détecter et d’apporter une réponse si un nœud tombe en panne.
- Endpoint controller permet de joindre les services et les pods.
- “Service Account and Token Controllers” gère les comptes et les jetons pour l’accès à l’API.

Il existe une communication entre les nodes et le panneau de contrôle à travers un appel gRPC.

Kubernetes possède un serveur API qui permet aux utilisateurs de contrôler les clusters. 

Kubernetes permet de gérer la durée de vie d’un pod et son arrêt. Un utilisateur peut également manager chaque pod pour changer les informations. Cependant certaines informations d’un pod sont immuables tel que son nom ou sa date de création.

Kubernetes possède également une garbage collection qui regroupe divers mécanismes pour garder en environnement propre. Par exemple, il est utilisé lorsque les pods se terminent ou pour supprimer les objets sans références à un propriétaire.

### Services

Kubernetes définit un service comme une méthode permettant d'exposer une application réseau qui s'exécute sous une ou plusieurs pods dans un cluster.

Sur chaque node, kube-proxy est exécuté, il permet de prendre en compte les services définis dans l’API Kubernetes de chaque nœud.

L’utilisation de mixed version proxy permet aux administrations d’un cluster de haute disponibilité de configurer ce cluster et de le mettre à jour de façon plus sûre en demandant les ressources vers le bon serveur API.

### Stockage des données

Toutes les informations du cluster sont stockées dans etcd, une base de données pour des paires clé-valeur. Les informations stockées peuvent être l’état actuel d’un cluster, les différentes configurations pour les ressources ou les données d’exécution. L’utilisation d’etcd permet d’avoir une consistance et une haute disponibilité des valeurs clés stockées pour toutes les données du cluster.

## Sécurité

La sécurité de Kubernetes est basée sur le Cloud Native Security. Cette sécurité se base sur la notion des 4C, chaque couche du modèle s’appuie sur la sécurité de sa couche externe.

La sécurité de Kubernetes se fait sur deux axes : 

- la sécurité des composants qui sont configurables dans un cluster.
- la sécurité des applications qui sont exécutés dans un cluster.

Kubernetes gère la sécurité des composants d’un cluster et non les applications qui sont exécutées ou les conteneurs.

### Authentification et autorisation

Kubernetes définit deux catégories d’utilisateurs, les comptes de services managés par Kubernetes et les utilisateurs normaux.

Les utilisateurs normaux sont :

- un administrateur distribuant les clés privées
- un user store, un dépôt de comptes utilisateurs et de credentials 
- un fichier avec une liste d’utilisateur et de mots de passe


Les utilisateurs normaux ne peuvent pas être ajoutés à un cluster à travers les appels API. Un utilisateur qui présente un certificat signé par la CA (certificate authority) du cluster est considéré comme authentifié. Chaque utilisateur appartient à un groupe, ce qui lui donne certains droits.

Kubernetes utilise les certificats en format X509 pour les connexions clients avec des jetons ou une authentification proxy pour authentifier des requêtes d’API. Pour éviter le spoofing, l’utilisation d’un proxy d’authentification est nécessaire dans le but de vérifier la validité du certificat présenté par le client.

Les static token sont un type d’authentification pour l’accès au serveur API. L’administrateur définit un ensemble de jetons d’accès, ces jetons sont liés à des métadonnées qui permettent de lier des utilisateurs à des groupes RBAC durant le processus d’autorisation.

Le service account tokens authentifie les jetons d’une requête faite à l’API serveur.

### Accès réseau

Tous les accès de Kubernetes ne sont pas publiquement alloués, on passe par la network access control list pour restreindre à un set d’adresses IP pour administrer le cluster. <br>
Par exemple, Les nodes sont configurées pour n’accepter que les connexions qui viennent de cette liste.

Lorsque l’accès est nécessaire, Kubernetes utilise OpenID Connect Tokens, un OAuth2  afin qu’une application puisse utiliser des ressources extérieures au nom d’un utilisateur.
Stockage sécurisé
Kubernetes définit une notion “Secrets” pour garder une information sensible comme les mots de passe ou les clés d’authentification. Il enregistre cette information dans un pod spécifique ou une image d’un conteneur. Les secrets et les crédentials ont une durée de vie courte. Kubernetes conseille d’encrypter les sauvegardes de la base de données etcd. 

Pour supprimer les jetons qui ne sont plus utilisés, Kubernetes appelle le daemon “kube-controller-manager” et en particulier la partie Service Account and Token Controllers.

Kubernet offre la possibilité de chiffrer les données qui ne sont pas utilisées. <br>
Ces solutions permettent de réduire les risques d’attaques.

## Limites de Kubernetes

### Limites matérielles

Kubernetes possède une limitation par la taille maximale des différentes composantes d’un cluster. Par exemple, dans une node il ne peut y avoir plus de 110 pods.

De plus, Kubernetes utilise la fonctionnalité cgroup afin que kubelet et les conteneurs en exécution ont besoin de groupes afin de répartir les différentes ressources.

### Failles

Une faille critique a été découverte en 2018. Un utilisateur pouvait se connecter au serveur API et envoyait des requêtes directement aux serveurs backend sans avoir de vérification par le serveur API.<br>
Les récentes failles trouvées sur Kubernetes touchent les Windows nodes. Ces failles permettent d’escalader en mode privilège sur des nodes qu’un utilisateur crée.

## Conclusion

Pour conclure, Kubernetes offre une solution afin d’articuler 

## Sources

- [Wikipedia](https://en.wikipedia.org/wiki/Kubernetes) - Kubernetes
- [GitHub](https://github.com/kubernetes) - Kubernetes
- [Wikipedia](https://en.wikipedia.org/wiki/Service_mesh) - Service mesh
- [Google](https://cloud.google.com/kubernetes-engine/docs/concepts/types-of-clusters?hl=fr#regional_clusters)  - Clouds regionaux
- [Kubernetes](https://kubernetes.io/docs/concepts/security/controlling-access/) - Controlling access
- [Stack Overflow](https://stackoverflow.com/questions/63827767/how-to-set-token-auth-file-somefile-flag-to-apiserver-on-kubernetes-v1-19-0) - How to set token auth file 
- [LEMONDEINFORMARIQUE](https://www.lemondeinformatique.fr/actualites/lire-la-faille-critique-kubernetes-corrigee-73674.html) - La faille critique de Kubernetes
- [CVEdetails.com](https://www.cvedetails.com/vulnerability-list/vendor_id-15867/product_id-34016/Kubernetes-Kubernetes.html) -  Kubernetes : Security Vulnerabilities, CVEs


gRPC : gRPC est un composant permettant aux logiciels de communiquer entre eux via HTTP en utilisant l'interface local de la machine, s’abstrayant ainsi de son environnement. 
ordonnanceur : l'ordonnance est le composant du noyau du système d'exploitation choisissant l'ordre d'exécution des processus sur les processeurs d'un ordinateur.
