Paths
=====

Organize your path definitions within this folder.  You will reference your paths from your main `openapi.yaml` entrypoint file.

It may help you to adopt some conventions:

* path separator token (e.g. `@`) or subfolders
* path parameter (e.g. `{example}`)
* file-per-path or file-per-operation

There are different benefits and drawbacks to each decision.  

You can adopt any organization you wish.  We have some tips for organizing paths based on common practices.


## Each operation in a separate file

You may also place each operation in a separate file.  

In this case, if you want all paths at the top-level, you can concatenate the http method to the path name.  Similar to the above option, you can 

### Files at top-level of `paths`

You may name your files with some concatenation for the http method. For example, following a convention such as: `<path with allowed separator>-<http-method>.yaml`.

#### Motivations

* Quickly see all operations without needing to navigate subfolders.

#### Drawbacks

* Adopting an unusual path separator convention, instead of using subfolders.

### Use subfolders to mirror API path structure

Example:
```
GET /customers

/paths/customers/get.yaml
```

In this case, the path id defined within subfolders which mirror the API URL structure.

Example with path parameter:
```
GET /customers/{id}

/paths/customers/{id}/get.yaml
```

#### Motivations

It matches the URL structure.

It is pretty easy to reference:

```yaml
paths:
  '/customers/{id}':
    get:
      $ref: ./paths/customers/{id}/get.yaml
    put:
      $ref: ./paths/customers/{id}/put.yaml
```

#### Drawbacks

If you have a lot of nested folders, it may be confusing to reference your schemas.  

Example
```
file: /paths/customers/{id}/timeline/{messageId}/get.yaml

# excerpt of file
    headers:
      Rate-Limit-Remaining: 
        $ref: ../../../../../components/headers/Rate-Limit-Remaining.yaml

```
Notice the `../../../../../` in the ref which requires some attention to formulate correctly.  While openapi-cli has a linter which suggests possible refs when there is a mistake, this is still a net drawback for APIs with deep paths.
