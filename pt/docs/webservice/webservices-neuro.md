Title: Webservices - NEURO
Description: Webservices Neuro

#Webservices Neuro

Este documento reúne os conhecimentos mínimos necessários para o adequado uso dos serviços via Rest.

| **URL**                                      | **Description**                           | **Parameters**                                | **Return**                                        |
|----------------------------------------------|-------------------------------------------|-----------------------------------------------|---------------------------------------------------|
| **/startWithMap/{name}**                     | Inicia um fluxo ESI a partir do nome                | name: Nome do fluxo cadastrado             | Objeto representativo do fluxo cadastrado      |
| **/instance/initialize/{processInstanceId}** | Recupera os valores de uma instância de processo | processInstanceid: ID da instância de processo | Objeto representativo da instância de processo cadastrada |
| **/instance/suspend/{processInstanceId}**    | Suspende uma instância de processo               | processInstanceid: ID da instância de processo | Objeto representativo da instância de processo cadastrada |
| **/instance/restart/{processInstanceId}**    | Reinicia uma instância de processo               | processInstanceid: ID da instância de processo | Objeto representativo da instância de processo cadastrada |
| **/instance/reopen/{processInstanceId}**     | Reabre uma instância de processo                | processInstanceid: ID da instância de processo | Objeto representativo da instância de processo cadastrada |
| **/userTask/{userTaskId}**                   | Recupera uma tarefa de usuário                    | userTaskId: ID da tarefa de usuário criada       | Objeto representativo da instância de processo cadastrada |
| **/rule/executeWithMap/{name}**              | Executa uma regra de negócio                  | name: Nome da regra de negócio cadastrada    | Objeto representativo da regra de negócio executada      |

-   request body: JSON Objeto com variáveis para o fluxo
-   request body: JSON Objeto com variáveis para regras de negócio

 **Tabela 1 - Especificação das APPLICATION PROGRAMMABLE INTERFACES (APIs)  

SPECIFIC GUIDELINES FOR REST API ACCESS
---------------------------------------

In the following sections, each type of intended use for the services available via Rest is detailed in the CITSMart Enteprise Neuro product.

###Authentication

To use the APIs, the client must be logged into Neuro. To do this, simply get a token from the authentication service and inject that token into the header of each REST request with the authentication-token identifier.
Authentication is done through the POST **/cit-esi-web/rest/security/token** service, passing a JSON object with the username and password attributes in the body.

![](../img/9181.png)

**Figure 1 - Example using the Chrome Restart Client plugin**

###Business Objects REST API

Each business object provides a set of REST services that can be consumed from the URL **/cit-esi-web/dynamic/{application name}/{business object name}**. These are basic services for creating, updating, listing and deleting the business object, as well as a getStructure method that returns the metadata of the business object. Each SQL created in the business object can also be called in method form.
Following are examples using the hotel business object of the hotels application. For each request, the authentication-token obtained in the login service must be provided. The url should end with ".json".

####Inclusion of business object

-   **HTTP verb**: POST
-   **URL:** /cit-esi-web/rest/dynamic/{application name}/{business object name}.json
-   **Body:** JSON containing the attributes of the business object record to be included

![](../img/9182.png)

**Figure 2 - Inclusion of business object**

####Business Object Change

-   **HTTP verb**: POST
-   **URL:** /cit-esi-web/rest/dynamic/{application name}/{business object name} /update.json
-   **Body:** JSON containing the attributes of the business object record to be changed

![](../img/9183.png)

**Figure 3 - Change of business object**

####Business Object Deletion

-   **HTTP verb: **POST
-   **URL:** /cit-esi-web/rest/dynamic/{application name}/{business object name}/remove.json
-   **Body: **JSON containing the primary key of the business object record to be deleted

![](../img/9184.png)

**Figure 4 - Business Object Deletion**

####Listing of business objects

-   **HTTP verb:** GET
-   **URL:** /cit-esi-web/rest/dynamic/{application name}/{business object name}.json

![](../img/9185.png)

**Figure 5 - Listing of business objects**

#### Business object recovery by primary key

-   **HTTP verb: **POST
-   **URL:** /cit-esi-web/rest/dynamic/{application name}/{business object name}/restore.json
-   **Body:** JSON containing:
    -   The primary key of the business object record to be deleted
    -   Attribute boolean findManyToOne, indicating whether the system should retrieve many relationships for one of the object
    -   Boolean findOneToMany attribute, indicating whether the system should retrieve one-to-many relationships from the object

![](../img/9186.png)

**Figure 6 - Recovery of business object by primary key**

#### Retrieving the structure of the business object

-   **HTTP verb: **GET
-   **URL:** /cit-esi-web/rest/dynamic/{application name}/{business object name}/getStructure.json

![](../img/9187.png)

**Figure 7 - Recovery of the structure of the business object**

#### SQL Execution of the Business Object

-   **HTTP verb**: POST
-   **URL:** / cit-esi-web / rest / dynamic / {application name} / {business object name} /list.son
-   **Body:** JSON containing:
    -   SQLName attribute with the name of the SQL to be executed
    -   JSON dynamicModel attribute containing the expected parameter values in SQL
    -   Attribute boolean findManyToOne (optional), indicating whether the system should retrieve many relationships for one of the object
    -   Attribute boolean findOneToMany (optional), indicating whether the system should retrieve one-to-many relationships from the object

![](../img/9188.png)

**Figure 8 - SQL execution of the business object**

### Execution of ESI streams

Any ESI stream can be executed via REST using the POST service **/cit-esi-web/rest/esi/execute/{stream name}**. For each request, the authentication-token obtained in the login service must be provided.
In the body of the request a JSON containing the input variables required to execute the flow must be provided.
The example below performs the job-search flow, providing the "12345" enrollment as the input variable of the stream.
The stream returns the "employee" JSON object.

![](../img/9189.png)

**Figure 9 - Example of running ESI streams**

<hr>
<font  Size=2><b>Produto/Versão:</b> CITSmart ESP | 8.00</font> &nbsp; &nbsp;
<font  Size=2><b>Atualização:</b>12/12/2018 - Andre Luiz de Oliveira Fernandes</font>
	






