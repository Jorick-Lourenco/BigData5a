# BigData5a
## Commandes de bases - 1er cours

**``docker run image_name``** : permet de créer et démarer une nouvelle instance de l'image (conteneur)

**``docker run -d image_name:tag``** : permet de démarrer conteneur en arrière plan

La valeur par défaut d'une image est latest.

**``docker ps``** : permet de lister tous les conteneurs qui sont en cours d'exécution. Chaque conteneur dispose d'un identifiant unique.

**``docker ps -a``** : permet de lister tous les conteneurs aver leurs statuts (Up, Exited Created)

**``docker run -d -p 9999:80 nginx``** : exécuter une image du serveur nginx.

Pour donner un nom précis à votre conteneur, on utilise **--name**. 

Exemple : ``docker run --name moningx -d -p 8989:80 nginx``, cette commande permet de démarrer un conteneur dont le nom est moningnx. Pour pouvoir y accéder de l'extérieur (du moteur), on utilise la notion de redirection des ports. Ici, on va utiliser le port 8989 pour y accéder comme ceci : localhost:8989

Pour arrêter un conteneur :  **``docker stop identifiant_conteneur``**. 

Pour supprimer un conteneur, on doit d'abord l'arrêter : **``docker rm identifiant_conteneur``**.

Pour lister toutes vos images :  **``docker images``**

Pour forcer la suppression d'une image ou d'un conteneur : **``docker rmi -f nom_image``**

``apt install default-jre`` -> installation JAVA

## Exercices 

La commande exec permet d'exécuter une commande dans un conteneur démarré.

Exemple : notre objectif est de démarrer un conteneur MySQL et d'exécuter le client mysql

Solution : **``docker run --name monserveur -e MYSQL_ROOT_PASSWORD=root -d mysql``**

Pour lancer mysql : **``docker exec -ti monserveur mysql --password``**

Exercice : démarer un conteneur à partir de l'image ubuntu et puis installer java et exécuter un programme simple.

**``docker run -it ubuntu bash``**

Une fois à l'intérieur du conteneur, vous pouvez installer les outils dont vous avez besoin comme vous avez l'habitube de le faire en utilisant ubuntu.

Si on veut installer par exemple Java, on procède de la façon suivante : on met d'abord à jour ``apt-get update`` puis ``apt-get install java``. Si on veut utiliser vim comme éditeur ``apt-get install vim``.

Pour inspecter un conteneur et avoir des détails sous forme d'un fichier json ``docker inspect id_conteneur``

Pour avoir les logs d'un contenur : **``docker logs id_conteneur``**

Pour redémarrer un conteneur : **``docker start id_conteneur``**

Exercice : démarrer un conteneur nginx et changer le contenu du fichier index.html.

La page index.html est par défaut dans le répertoire /usr/share/nginx/html


docker volume

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes
  
Pour faire **"mapping volume"**

**``docker run -dti -p 8080:80 -v /Users/syoucef/Desktop/dockerexample/mapage/:/usr/share/nginx/html/ --name myserverweeb nginx``**

**``docker run -tid --name webvolumebis -p 8691:80 --mount source=mesdonnees,target=/usr/share/nginx/html/ nginx``**

Pour accéder aux volumes (sous mac) :

**``docker run -it --privileged --pid=host debian nsenter -t 1 -m -u -n -i sh``**

Une fois à l'intérieur du conteneur, les volumes se trouvent dans le dossier : /var/lib/docker/volumes/

La solution ici : https://stackoverflow.com/questions/38532483/where-is-var-lib-docker-on-mac-os-x

**Gestion des variables d'environnement :**

**``docker run -tid --name variableenvironnement --env MAVARIABLE="samir1234" ubuntu:latest``**

Passer une variable d'environnement en ligne de commande : **``docker exec -ti variableenvironnement sh``**

On peut asussi passer un ensemble de variables d'environnement sauvegardé dans un fichier :

**``docker run -tid --name fichiervariableenvironnement --env-file mesvariables.lst ubuntu:latest``**

Définir sa propre image Docker :

#### Docker commit

Exemple : partir d'une image ubuntu, installer python et vim et ajouter queqlues variables d'environnemnetsauvegardées dans un fichier.

1. On commence par créer un fichier env.lst (on définit autant de variables souhaitées)

2. Pour ajouter ces variables d'environnement **``docker run --name monconteneurvariables -tdi --env-file env.lst ubuntu``**

3. Pour installer python et vim, on rentre à l'intérieur du conteneur docker exec -ti monconteneurvariables sh

Pour installer python et vim : ``apt-get update``, ''apt-get install python et `àpt-get install vim``

4. On quite le conteneur ``exit``

5. Pour créer une image à partir de ce conteneur ``docker commit -m "créeation d'une image ubuntu augumentée" id_conteur nom_notre_image``

#### Dockerfile

1. Créer un fichier Dockerfile 

2. On part d'une image existente (la plus petite est alpine), copier éventuellemnet un programme et l'exécuter ....

Exemple :

FROM openjdk
COPY Application.class Application.class
CMD ["java", "Application"]

Pour Python :

* FROM ubuntu
* COPY application.py application.py
* RUN apt-get update
* RUN apt-get install python -y
* CMD ["python", "application.py"]
* FROM : permet de définir l'image source
* CMD : la commande par défautt lors de l'exécution du conteneur
* EXPOSE : permet de définir le port d'écoute par défaut
* RUN : permet d'exécuter des commandes à l'intérieur du conteneur
* ADD : permet d'ajouter des fichier dans le conteneur
* VOLUME : permet de définir les volumes utilisables

Mes commandes :

**``docker build -t imagejorick .``**

**``docker run image jorick``**

Publier une image sur DockerHub :

1. Créer son image, soit josephadd
2. Créer un dépôt dockerHub syoucef/joseph
3. Se connecter à DockerHub via ligne de commande : docker login (facultatif si on utilise docker desktop)
4. Créer un lien entre l'image josephaddet syoucef/joseph : docker tag josephadd syoucef/joseph
5. Envoyer l'image vers Dockerhub docker push syoucef/joseph

Pour chercher une image par ligne de commande ``docker serach nom_image``

 **``docker tag imagejorick lourencoj/syoucefjoseph``**
 
 **``docker push lourencoj/syoucefjoseph``**
 
 Disponible via : ``docker pull lourencoj/syoucefjoseph``
 

Pour Nginx : 
Dockerfile :

* FROM nginx
* COPY index.html /usr/share/nginx/html
* EXPOSE 80

index.html :

--------------------------------------

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Salut les nazes</title>
</head>
<body>

</body>
</html>

---------------------------------------

**``docker build -t imagejorick .``**

**``docker run -d -p 8080 imagejorick``**

## 2ème Cours 

MongoDB est un système de gestion de base de données documentaire.

Une base de données documentaire est, à la différence d'une base de données relationnelle, orientée sur les documents. Il n'existe pas de "tables" avec des relations entre-elles, ici, il s'agit de documents sous tout type de forme. Les documents ne nécessitent pas d'avoir tous les mêmes champs, ce qui permet une grande flexibilité.

Ce type de base de données est donc idéal lorsque les données prennent différentes formes ou que les données sont très volumineuses.

Utilisation de l'image Docker de MongoDB: **``docker run --name mongodb -d -p 27017:27017 mongo``**
Il est possible d'accéder à la CLI MongoDB avec la commande suivante: **``docker exec -ti *containerID* mongo``**

#### Commandes MongoDB utiles

* use produit: créé une base de données intitulée produit
* db.createCollection("test"): créé une collection intitulée test
* db.test.insert({"propriete":"valeur"}): ajoute un objet à la collection test, possedant une propriété propriete ayant la valeur valeur
* show collections: affiche les collections
* db.test.find(): affiche tous les éléments contenus dans la collection test
* db.test.find({"propriete":"autrevaleur"}): affiche l'objet possédant la propriété propriete égale à la valeur autrevaleur

## Exercice à rendre

On va devoir développer un micro-service qui utilise la persistance des données MongoDB. Par la suite, on va créer une image Docker de notre propre service. Le lien se trouvera au début du readme afin de la télécharger.

#### Création du service 

On va utiliser Intellij Idea Ultimate pour réaliser un nouveau projet Spring. On va utiliser deux dépendances principalement : ``Spring Web`` et ``MongoDB``.

Nous allons créer une interfae implémentant ``MongoRepository`` que l'on va appeler ``ProduitRepository`` :

```java

package com.example.demo.produit;

import com.example.demo.produit.Produit;
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ProduitRepository extends MongoRepository<Produit, String> {
    Produit findByDescription(String description);
    
```

Nous allons nous baser sur un modèle de stockage de produit :

```java

package com.example.demo.produit;


import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;

import java.io.Serializable;

@Document(collection = "produit")
public class Produit implements Serializable {
    @Id
    private String id;

    @Field("description")
    private String description;

    @Field("prix")
    private double prix;

    public Produit(String id, String description, double prix){
        this.id = id;
        this.description = description;
        this.prix = prix;
    }
}

```

* ``@Document`` ( collection = "produit" ): symbolise la collection utilisée, ici ``produit`` créer directement dans MongoDB et qui contient 2 produits.
* ``@Id`` : identifiant du document, généré de façon automatique.
* ``@Field("description")`` : indication que cet attribut est converti en tant que clé/vlaeur nommée description dans les objets de la collecton ``produit`` de MongoDB.

Maintenant, nous allons réaliser le Mapping qui va s'occuper de répondre à nos requêtes HTTP. Le contrôleur/service s'appelera ProduitService :

```java

package com.example.demo.produit;

import com.example.demo.produit.Produit;
import com.example.demo.produit.ProduitRepository;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Collection;

@RestController
@RequestMapping("/produit")
public class ProduitService {
    private final ProduitRepository produitRepository;

    public ProduitService(ProduitRepository produitRepository){
        this.produitRepository = produitRepository;

    }

    @GetMapping("/{description}/{prix}")
    public Produit add(@PathVariable String description, @PathVariable double prix)
    {
        if(!description.isEmpty())
        {
            return produitRepository.insert(new Produit(null, description, prix));
        }
        else
        {
            return null;
        }
    }

    @GetMapping("/{description}")
    public Produit getById(@PathVariable String description )
    {
        return produitRepository.findByDescription((description));
    }

    @GetMapping
    public Collection<Produit> get() {
        return produitRepository.findAll();
    }

}

```

Dans ce contrôleurn nous utilisons du ``GetMapping`` (GET). De plus, afin de pouvoir interagir avec la base de données MongoDB, nous avons besoin d'instancier notre ProduitRepository créé précédemment.

Dans notre projet, nous utilisons les méthodes suivantes:

* findAll(): permet de renvoyer l'intégralité des objets dans la collection produit
* insert(Produit p): permet d'insérer un nouvel objet dans la base de données
* findByDescription(description): permet de faire une recherche sur la collection produit pour trouver un objet possédant une description identique à description

Enfin, nous renseignons ensuite nos informations MongoDB dans les propriétés de l'application, dans ``application.properties`` :

```java

spring.data.mongodb.host=monconteneurmongo
spring.data.mongodb.database=produit

```

* La première ligne indique que nous utilisons un conteneur appelé ``mongodb``
* La seconde ligne indique que notre base de données s'appelle ``produit``

Pour pouvoir faire fonctionner le tout, nous devons effectuer un ``clean`` et un ``install`` directement dans Intellij Idea.

#### Création du Dockerfile

Nous allon créer un **Dockerfile** qui nous servira à concevoir l'image contenant notre service :

```java

FROM openjdk:8
ADD target/projetmongo.jar projetmongo.jar
CMD ["java", "-jar", "projetmongo.jar"]
EXPOSE 8080


```

#### Lancement

Nous commençons par lancer un conteneur MongoDB grâce à la commande suivante: ``docker run --name mongodb -d -p 27017:27017 mongo.`` Le port d'écoute de MongoDB est alors 27017.
Ensuite, nous pouvons construire l'image de notre service grâce au Dockerfile créé précédemment: ``docker build -t servicemongodb .`` (L'image aura donc pour nom: service). Maintenant nous lançons un conteneur de notre service, en spécifiant que nous faisons un lien avec le conteneur MongoDB: ``docker run -p 8282:8080 --link monconteneurmongo:monconteneurmongo -d servicemongodb``.

Notre service est donc disponible sur : http://localhost:8282/produit

On peut effectuer différentes requêtes :

* ``GET`` http://localhost/produit : liste l'ensemble des produits de la collection.
* ``GET`` http://localhost/produit/description : renvoit l'objet correspondant à la description.
* ``GET`` POST http://localhost/produit/{description}/{prix} : ajoute un nouveau produit ayant une description description et un prix prix dans la base de données.

#### Upload de l'image sur Docker Hub

Pour finir, nous allons envoyer notre image sur Docker Hub. Pour ce faire, nous allons utiliser les commandes suivantes :

* ``docker tag service leokr/syoucef-mongodbservice`` : permet de faire le lien entre l'image service et le Repository leokr/syoucef-mongodbservice
* ``docker push leokr/syoucef-mongodbservice``: permet d'envoyer la dernière version de l'image sur Docker Hub



