## Projet CI/CD
Ce dépôt est mon rendu pour le projet du cours de CI/CD. L'objectif de ce projet est de mettre en place une pipeline permettant la livraison continue du projet. 

## Application
Le projet est tiré des exemples du projet [Dropwizard](https://github.com/dropwizard/dropwizard).

Cette application comprend un exemple du module optionnel DB API. Les exemples fournis illustrent quelques les fonctionnalités disponibles dans [Hibernate](http://hibernate.org/), ainsi que la démonstration de leur utilisation à l'intérieur Dropwizard.

Cet exemple de base de données comprend plusieures classes de Dropwizard. Pour plus d'information : [README](https://github.com/dropwizard/dropwizard/blob/master/dropwizard-example/README.md). 

## Utilisation de l'application
Pour tester l'exemple, suivre les indications ci-dessous :

* Pour construire l'exemple utiliser  [Apache Maven](https://maven.apache.org/)  pour packager le projet :

        cd dropwizard
        ./mvnw package
        cd dropwizard-example

* Pour mettre en place la base de données h2 : 

        java -jar target/dropwizard-example-$DW_VERSION.jar db migrate example.yml

* Pour lancer le server : 

        java -jar target/dropwizard-example-$DW_VERSION.jar server example.yml

* Pour avoir l'exemple Hello World :

	http://localhost:8080/hello-world

* Pour envoyer des données à l'application :

	curl -H "Content-Type: application/json" -X POST -d '{"fullName":"Other Person","jobTitle":"Other Title"}' http://localhost:8080/people
	
	Ouvrir http://localhost:8080/people
