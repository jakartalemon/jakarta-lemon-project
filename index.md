# Jakarta Lemon Project v.1.2

- Source [https://bitbucket.org/jakarta-lemon/jakarta-lemon-plugin/](https://bitbucket.org/jakarta-lemon/jakarta-lemon-plugin/)
- Maven [https://central.sonatype.com/artifact/dev.jakartalemon/jakarta-lemon-plugin/1.2](https://central.sonatype.com/artifact/dev.jakartalemon/jakarta-lemon-plugin/1.2)

## Contents
- [Summary](#summary)
- [How to create the Jakarta EE app with Jakarta Lemon](#how-to-create-the-jakarta-ee-app-with-jakarta-lemon)
  - [Creating Persistence and Service layer](#creating-persistence-and-service-layer)
  - [Creating RESTful service](#creating-restful-service)
  - [Creating View Layer with JSF](#creating-view-layer-with-jsf)

---

# Summary

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

### `model.json` File
The file structure is similar to this:

```json
{
    "datasource": {
        "db": "mysql",
        "url": "jdbc:mysql://localhost/test",
        "user": "test",
        "password": "test",
        "properties":{
            "serverTimezone":"America/Lima",
            "allowPublicKeyRetrieval":"true",
            "useSSL":"false"
        },
        "style":"payara-resources"
    },
    "entities": [
        {
            "name":"ProductCategory",
            "tableName":"prod_cat",
            "fields":{
                "categoryId":{
                    "type":"Long",
                    "pk":true,
                    "generatedValue":"identity"
                },
                "category":{
                    "type":"String",
                    "length":50,
                    "columnName":"category_name"
                }                
            }
        },
        {
            "name":"Product",
            "fields":{
                "productId": {
                    "type": "Long",
                    "pk": true,
                    "generatedValue":"identity",
                    "columnName":"product_id"
                },
                "name":{
                    "type":"String",
                    "columnName":"product_name"
                },
                "category":{
                    "type":"ProductCategory",
                    "join":"ManyToOne",
                    "columnName":"category_id"
                }
            }
        },
        {
            "name": "Customer",
            "fields": {
                "customerId": {
                    "type": "Long",
                    "pk": true,
                    "generatedValue":"identity"
                },
                "firstName": {
                    "type": "String",
                    "columnName": "first_name",
                    "length": 30
                },
                "lastName": {
                    "type": "String",
                    "columnName": "last_name",
                    "length": 30
                },
                "emailAddress": {
                    "type": "String",
                    "columnName": "email_address",
                    "length": 100
                }
            }
            ,
            "finders":  
                    {
                        "LikeName": {
                            "query": "select c from Customer c where c.firstName like :firstName",
                            "parameters": 
                                    {
                                        "firstName": "String"
                                    },
                            "return": "java.util.List<Customer>"
                        },
                        "Email": {
                            "query": "select c from Customer c where c.emailAddress = :emailAddress",
                            "parameters":  
                                    {
                                        "emailAddress": "String"
                                    },
                            "unique": true,
                            "return": "Customer"
                        }
                    }
        }
    ]
}
```
Values for the datasource > style field
- payara-resources
- web.xml
- openliberty


## Creating RESTful service

The `openapi.json` file indicates the structure of the entities to use


```
mvn jakarta-lemon:create-rest
```

### `openapi.json` file

The `openapi.json` file conforms to the [OpenAPI](https://www.openapis.org/) standard, only for now it uses the JSON format.

The content is similar to this:
```json
{
    "openapi": "3.0.0",
    "info": {
        "title": "Deployed Resources",
        "version": "1.0.0"
    },
    "servers": [
        {
            "url": "http:/myhost:8080/example-project",
            "description": "Default Server."
        },
        {
            "url": "https:/myhost:8181/example-project",
            "description": "Default Secured Server."
        }
    ],
    "paths": {
        "/api/customer": {
            "post": {
                "summary": "a summary",
                "description": "a description",
                "operationId": "create",
                "requestBody": {
                    "content": {
                        "application/json": {
                            "schema": {
                                "$ref": "#/components/schemas/CustomerRequest"
                            }
                        }
                    }
                },
                "responses": {
                    "default": {
                        "content": {
                            "application/json": {
                                "schema": {
                                    "type": "object"
                                }
                            }
                        },
                        "description": "Default Response."
                    }
                },
                "deprecated": false
            }
        },
        "/api/employee": {
            "get": {
                "summary": "a summary",
                "description": "a description",
                "operationId": "list",
                "responses": {
                    "default": {
                        "content": {
                            "application/json": {
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/components/schemas/EmployeeResponse"
                                    }
                                }
                            }
                        },
                        "description": "Employee list"
                    }
                },
                "deprecated": false
            }
        },
        "/api/employee/{id}": {
            "get": {
                "summary": "a summary",
                "description": "a description",
                "operationId": "aEmployee",
                "parameters": [
                    {
                        "in": "path",
                        "name": "id",
                        "required": true,
                        "schema": {
                            "type": "integer"
                        }
                    }
                ],
                "responses": {
                    "default": {
                        "content": {
                            "application/json": {
                                "schema": {
                                    "$ref": "#/components/schemas/EmployeeResponse"
                                }
                            }
                        },
                        "description": "a Employee Info"
                    }
                },
                "deprecated": false
            }
        }
    },
    "components": {
        "schemas": {
            "CustomerRequest": {
                "type": "object",
                "properties": {
                    "firstName": {
                        "type": "string"
                    },
                    "lastName": {
                        "type": "string"
                    }
                }
            },
            "EmployeeResponse": {
                "type": "object",
                "properties": {
                    "firstName": {
                        "type": "string"
                    },
                    "lastName": {
                        "type": "string"
                    },
                    "employeeId": {
                        "type": "integer"
                    }
                }
            },
            "CustomerResponse": {
                "type": "object",
                "properties": {
                    "firstName": {
                        "type": "string"
                    },
                    "lastName": {
                        "type": "string"
                    },
                    "customerId": {
                        "type": "integer"
                    }
                }
            }
        }
    }
}
```

## Creating View Layer with JSF

The `view.json` file indicates the structure of the entities to use


```
mvn jakarta-lemon:create-view
```
### `view.json` file

This file allows you to create views from a configuration file. 
For now create the views using [Jakarta Faces (JSF)](https://jakarta.ee/specifications/faces/)
It also includes field validation.

The content is similar to this:
```json
{
    "style": "jsf",
    "paths": {
        "/customers": {
            "type": "list",
            "type2": "form",
            "formBean": "customer",
            "editForm": "/customerForm"
        },
        "/customerForm": {
            "type": "form",
            "formBean": "customer",
            "listView": "/customers"
        }
    },
    "formBeans": {
        "customer": {
            "firstName": {
                "notNull": {
                    "message":"Este campo no puede ser nulo"
                },
                "notEmpty": true,
                "type": "String",
                "label": "Nombres"
            },
            "birthDate": {
                "type": "LocalDate",
                "pastOrPresent": true,
                "label": "Fecha de nacimiento"
            },
            "weight": {
                "type": "float",
                "decimalMax": {
                    "value": 120
                },
                "decimalMin": {
                    "value": 40
                },
                "label": "Peso"
            },
            "email": {
                "email": true,
                "label": {
                    "es": "correo electrónico",
                    "en": "e-email"
                }
            },
            "dni": {
                "digits": {
                    "integer": 8,
                    "fraction": 0
                },
                "size": {
                    "max": 10,
                    "min": 8
                },
                "label": "DNI",
                "primary": true
            }
        }
    }
}

```