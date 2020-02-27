
## [](https://github.com/elb29/ProjCICD#projet-cicd)Projet CI/CD

Ce dépôt est mon rendu pour le projet du cours de CI/CD. L'objectif de ce projet est de mettre en place une pipeline permettant la livraison continue du projet.

## Application

Le projet est tiré des exemples du projet [Dropwizard](https://github.com/dropwizard/dropwizard).

Cette application comprend un exemple du module optionnel DB API. Les exemples fournis illustrent quelques les fonctionnalités disponibles dans [Hibernate](http://hibernate.org/), ainsi que la démonstration de leur utilisation à l'intérieur Dropwizard.

Cet exemple de base de données comprend plusieures classes de Dropwizard. Pour plus d'information : [README](https://github.com/dropwizard/dropwizard/blob/master/dropwizard-example/README.md).

## Utilisation de l'application

Pour tester l'exemple, suivre les indications ci-dessous :

-   Pour construire l'exemple utiliser [Apache Maven](https://maven.apache.org/) pour packager le projet :
    
    ```
      cd dropwizard
      ./mvnw package
      cd dropwizard-example
    
    ```
    
-   Pour mettre en place la base de données h2 :
    
    ```
      java -jar target/dropwizard-example-$DW_VERSION.jar db migrate example.yml
    
    ```
    
-   Pour lancer le server :
    
    ```
      java -jar target/dropwizard-example-$DW_VERSION.jar server example.yml
    
    ```
    
-   Pour avoir l'exemple Hello World :
    
    [http://localhost:8080/hello-world](http://localhost:8080/hello-world)
    
-   Pour envoyer des données à l'application : `curl -H "Content-Type: application/json" -X POST -d '{"fullName":"Other Person","jobTitle":"Other Title"}' http://localhost:8080/people`
    
    Ouvrir [http://localhost:8080/people](http://localhost:8080/people)
    

## Pipeline

Pour ce projet j'ai choisi d'utiliser l'outil d'intégration continue Github Action avec la fonctionnalité Java maven project.

Cette [pipeline](https://github.com/elb29/ProjCICD/blob/master/.github/workflows/maven.yml) est exécuté à chaque Push et Pull request effectué. La pipeline est divisée en quatre étapes : Build, Tests in Java 8, Tests in Java 11 et Deploy. Comme schématisée sur le dessin plus bas après le Build, les deux étapes de tests sont effectuées parallèlement puis à la fin des deux phases de tests la phase Deploy est lancée. Les erreurs bloquant le processus de pipeline, elles empêchent aussi les pull request provocant des erreurs de se finir.

[![Schéma de la pipeline](https://github.com/elb29/ProjCICD/raw/master/img/pipeline.png)](https://github.com/elb29/ProjCICD/blob/master/img/pipeline.png)

## Phase Build

La phase build permet de tester si le projet est compilable : `mvn compile --file pom.xml` . Si elle échoue alors la pipeline est arrêtée. Elle effectue aussi la commande `mvn site`.

## Phases de Tests

Les phases de tests permettent d'effectuer les différents tests sous Java 8 et sous Java 11. Pour cela on attend tout d'abord que la phase précédente (Build) soit finie grâce à `needs : [build]`. Puis on spécifie la version de java sous laquelle effectuer les tests grâce à un setup du JDK :

```
name: Set up JDK 1.11 
	uses: actions/setup-java@v1
	with:
		java-version: 1.11 

```

Pour lancer les tests, on utilise la commande maven `run: mvn test --file pom.xml`. Si l'une de ces sous-étapes échoue, le pipeline s'arrête.

## Phase de Deploy

Dans la phase de Deploy on utilise la commande : `mvn -B javadoc:javadoc --file pom.xml` afin de créer la documentation statique de notre projet.

J'ai ensuite voulu lancer les commandes demandées dans la documentation du projet afin de préparer la h2 database et lancer le serveur :

```
- name : Setup h2 database
	run : java -jar target/dropwizard-example-2.0.3-SNAPSHOT.jar db migrate example.yml
- name : Server Run
	run : java -jar target/dropwizard-example-2.0.3-SNAPSHOT.jar server example.yml &

```

Cependant je n'ai pas réussi à faire fonctionner dans ma pipeline cette partie du code j'ai donc commenté ces commandes.

Puis on fini la phase Deploy avec la commande maven `run: mvn deploy`

## Branches de test de la pipeline
J'ai utilisé les branches test_pipeline afin de tester ma pipeline sur des pull request définies pour passer ou pour fail. 

 - FunctionnalPullRequest : on ajoute juste un commentaire sur script d'un pull master qui a passé la pipeline. 
 - NonFunctionnalPullRequest : on sabote un script qui ne passera pas l'étape build et la pipeline bloque alors la pull request.
 
## Problèmes rencontrés
 - J'ai eu du mal à trouver un projet personnel convenable (tests écrits et fonctionnels), j'ai donc pris comme projet un projet que vous aviez proposé à d'autres élèves de la classe. 
 - Je me suis rendu compte bien trop tard  dans l'avancement de mon projet (env. 5h) que certaines parties de ma pipeline étaient automatiquement sautées/simulées par le pom.xml qui configure le saut du deploy notamment (`<maven.deploy.skip>true</maven.deploy.skip>`) . En retirant les options qui permettent de sauter les parties concernées il se trouve que le projet ne passait plus ma pipeline. Après plusieurs tentatives de correction du problème, j'ai choisit de remettre la "simulation" du deploy afin d'avoir une pipeline qui passe le projet grâce à cette simulation et qui continue à tester les autres parties plutôt qu'une pipeline qui ne passerait aucun push/pull request a cause de ce problème tout en étant conscient que à cause de cette "simulation" de la partie deploy ma pipeline ne testes pas le deployement du projet.

