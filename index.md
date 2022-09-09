## Jakarta Lemon Project

This project helps the developer to create [Jakarta EE](https://jakarta.ee/) applications faster, saving time in configuring them.

With the help of a command and a configuration file, it will allow you to create the database configuration (Including the DataSource and the JPA).

With an `openapi.json` file it will allow you to create the rest services.

And with another config file and another command, it will allow you to create a basic CRUD using JSF.

Then, everything is in your hands so you can put the logic you need, modify what you want and adjust it to suit you.

# How to create the Jakarta EE app with Jakarta Lemon

Using [Maven](https://maven.apache.org/):

```
mvn -DarchetypeGroupId=dev.jakartalemon \
    -DarchetypeArtifactId=jakarta-lemon-archetype \
    org.apache.maven.plugins:maven-archetype-plugin:generate
```

## Creating Persistence and Service layer

The `model.json` file indicates the structure of the entities to use


```
mvn jakarta-lemon:create-model
```

## Creating RESTful service

The `open.json` file indicates the structure of the entities to use


```
mvn jakarta-lemon:create-rest
```


## Creating View Layer with JSF

The `view.json` file indicates the structure of the entities to use


```
mvn jakarta-lemon:create-view
```

