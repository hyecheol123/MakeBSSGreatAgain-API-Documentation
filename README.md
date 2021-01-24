# Make BSS Great Again OpenAPI Definition

This repository contains [ReDoc](https://github.com/Redocly/redoc) resources (complying OpenAPI specification) for the RESTful API documentation of **Make BSS Great Again** project.  
With the project, we are going to build the **alumni website of Busan Science High School**.  
List of features and API specification are under review.


## Working on your OpenAPI Definition

### Install

1. Install [Node JS](https://nodejs.org/).
2. Clone this repo and run `npm install` in the repo root.

### Usage

#### `npm start`
Starts the reference docs preview server.

#### `npm run build`
Bundles the definition to the dist folder.

#### `npm test`
Validates the definition.


## Contribution Guide

### Schemas

#### Adding Schemas

1. Navigate to the `openapi/components/schemas` folder.
2. Add a file named as you wish to name the schema.
3. Define the schema.
4. Refer to the schema using the `$ref` (see example below).

##### Example Schema
This is a very simple schema example:
```yaml
type: string
description: The resource ID. Defaults to UUID v4
maxLength: 50
example: 4f6cf35x-2c4y-483z-a0a9-158621f77a21
```
This is a more complex schema example:
```yaml
type: object
properties:
  id:
    description: The customer identifier string
    readOnly: true
    allOf:
      - $ref: ./ResourceId.yaml
  websiteId:
    description: The website's ID
    allOf:
      - $ref: ./ResourceId.yaml
  paymentToken:
    type: string
    writeOnly: true
    description: |
      A write-only payment token; if supplied, it will be converted into a
      payment instrument and be set as the `defaultPaymentInstrument`. The
      value of this property will override the `defaultPaymentInstrument`
      in the case that both are supplied. The token may only be used once
      before it is expired.
  defaultPaymentInstrument:
    $ref: ./PaymentInstrument.yaml
  createdTime:
    description: The customer created time
    allOf:
      - $ref: ./ServerTimestamp.yaml
  updatedTime:
    description: The customer updated time
    allOf:
      - $ref: ./ServerTimestamp.yaml
  tags:
    description: A list of customer's tags
    readOnly: true
    type: array
    items:
      $ref: ./Tags/Tag.yaml
  revision:
    description: >
      The number of times the customer data has been modified.

      The revision is useful when analyzing webhook data to determine if the
      change takes precedence over the current representation.
    type: integer
    readOnly: true
  _links:
    type: array
    description: The links related to resource
    readOnly: true
    minItems: 3
    items:
      anyOf:
        - $ref: ./Links/SelfLink.yaml
        - $ref: ./Links/NotesLink.yaml
        - $ref: ./Links/DefaultPaymentInstrumentLink.yaml
        - $ref: ./Links/LeadSourceLink.yaml
        - $ref: ./Links/WebsiteLink.yaml
  _embedded:
    type: array
    description: >-
      Any embedded objects available that are requested by the `expand`
      querystring parameter.
    readOnly: true
    minItems: 1
    items:
      anyOf:
        - $ref: ./Embeds/LeadSourceEmbed.yaml

```

##### Using the `$ref`

Notice in the complex example above the schema definition itself has `$ref` links to other schemas defined.

Here is a small excerpt with an example:

```yaml
defaultPaymentInstrument:
  $ref: ./PaymentInstrument.yaml
```

The value of the `$ref` is the path to the other schema definition.

You may use `$ref`s to compose schema from other existing schema to avoid duplication.

You will use `$ref`s to reference schema from your path definitions.

#### Editing Schemas

1. Navigate to the `openapi/components/schemas` folder.
2. Open the file you wish to edit.
3. Edit.


### Paths

#### Adding a Path

Individual files in the subdirectories of the `openapi/paths` represent a HTTP request (operation)

Example:
```
GET /customers

/paths/customers/get.yaml
```

Example with path parameter:
```
GET /customers/{id}

/paths/customers/{id}/get.yaml
```

Conventions for organizing paths:
* path separator token (e.g. `@`) or subfolders
* path parameter (e.g. `{example}`)

After you write the path file indicating a specific operation, add the path and a ref to it inside of your `openapi.yaml` file inside of the `openapi` folder.

The `paths` description in the `openapi.yaml` represents the URL structure.  
Example addition to the `openapi.yaml` file:
```yaml
paths:
  '/customers/{id}':
    get:
      $ref: ./paths/customers/{id}/get.yaml
    put:
      $ref: ./paths/customers/{id}/put.yaml
```

Here is an example of a YAML files at `paths/customers/{id}` directory:

`get.yaml`:
```yaml
get:
  tags:
    - Customers
  summary: Retrieve a list of customers
  operationId: GetCustomerCollection
  description: |
    You can have a markdown description here.
  parameters:
    - $ref: ../components/parameters/collectionLimit.yaml
    - $ref: ../components/parameters/collectionOffset.yaml
    - $ref: ../components/parameters/collectionFilter.yaml
    - $ref: ../components/parameters/collectionQuery.yaml
    - $ref: ../components/parameters/collectionExpand.yaml
    - $ref: ../components/parameters/collectionFields.yaml
  responses:
    '200':
      description: A list of Customers was retrieved successfully
      headers:
        Rate-Limit-Limit:
          $ref: ../components/headers/Rate-Limit-Limit.yaml
        Rate-Limit-Remaining:
          $ref: ../components/headers/Rate-Limit-Remaining.yaml
        Rate-Limit-Reset:
          $ref: ../components/headers/Rate-Limit-Reset.yaml
        Pagination-Total:
          $ref: ../components/headers/Pagination-Total.yaml
        Pagination-Limit:
          $ref: ../components/headers/Pagination-Limit.yaml
        Pagination-Offset:
          $ref: ../components/headers/Pagination-Offset.yaml
      content:
        application/json:
          schema:
            type: array
            items:
              $ref: ../components/schemas/Customer.yaml
        text/csv:
          schema:
            type: array
            items:
              $ref: ../components/schemas/Customer.yaml
    '401':
      $ref: ../components/responses/AccessForbidden.yaml
  x-code-samples:
    - lang: PHP
      source:
        $ref: ../code_samples/PHP/customers/get.php
```

`post.yaml`:
```yaml
post:
  tags:
    - Customers
  summary: Create a customer (without an ID)
  operationId: PostCustomer
  description: Another markdown description here.
  requestBody:
    $ref: ../components/requestBodies/Customer.yaml
  responses:
    '201':
      $ref: ../components/responses/Customer.yaml
    '401':
      $ref: ../components/responses/AccessForbidden.yaml
    '409':
      $ref: ../components/responses/Conflict.yaml
    '422':
      $ref: ../components/responses/InvalidDataError.yaml
  x-code-samples:
    - lang: PHP
      source:
        $ref: ../code_samples/PHP/customers/post.php
```

You'll see extensive usage of `$ref`s in this example to different types of components including schemas.

You'll also notice `$ref`s to code samples.


### Reusable Components
* You can create the following folders in `openapi/components` directory:
  - `schemas` - reusable [Schema Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#schemaObject)
  - `responses` - reusable [Response Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#responseObject)
  - `parameters` - reusable [Parameter Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#parameterObject)
  - `examples` - reusable [Example Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#exampleObject)
  - `headers` - reusable [Header Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#headerObject)
  - `requestBodies` - reusable [Request Body Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#requestBodyObject)
  - `links` - reusable [Link Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#linkObject)
  - `callbacks` - reusable [Callback Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#callbackObject)
  - `securitySchemes` - reusable [Security Scheme Objects](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md#securitySchemeObject)
* Filename of files inside the folders represent component name, e.g. `Customer.yaml`