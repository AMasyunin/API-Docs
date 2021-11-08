---
title: API Keys
description: Working with API keys 
---

# API Keys

The API key is the main thing that is needed for the integration.
This is the same as the hash of the user's session gotten by the [auth call](./user/index.md#auth),
only with an infinite lifetime. 

Unlike the user's session:

* the API key will not be deleted if the user logs out or changes the password,
* you don't need to [renew](./user/session/index.md#renew) the key periodically,
* you don't transfer or store the username and password,
* you can delete the key at any time if there is a suspicion of compromise.

!!! warning "Session hash is deprecated"
    To work with the API, it is necessary to use the API key, not the user's session hash. 
    Work with API through the user's session is deprecated and will be disabled in the future.
    The only thing that API calls with a user session will work for is creating,
    reading, and deleting API keys.

In one user's account, you can have up to 20 API keys intended for different external integrations. 
To distinguish keys from each other, you should give them meaningful names.

!!! warning "Security"
    Do not publish API keys anywhere. having a key, you can perform almost any action in the 
    user's account. Make API calls only over HTTPS because the key is transmitted in cleartext.

***

## API Key object

```json
{
  "hash": "c915157ac483e7319b0b257408bc04e1",
  "create_date": "2021-10-29 12:00:36",
  "title": "Integration with My Super App"
}
```

* `hash` - string, 32 chars. Hash of an API key.
* `create_date` - `date/time`. Key creation date.
* `title` - string. Key title.

***

## Actions

API path: `/api/key`.

### create

Creates a new API key.

This call is available only to the master user and only with a standard session
obtained using a login/password via [/user/auth](./user/index.md#auth).

#### parameters

| name  | description | type  | restrictions |
| :---- | :----       | :---- | :----        |
| hash | Master user's session hash. | String | Not empty. |
| title | New key title | String | Not empty, only printable characters. Max length: 255. |

#### examples

=== "cURL"

    ```shell
    curl -X POST '{{ extra.api_example_url }}/api/key/create' \
        -H 'Content-Type: application/json' \ 
        -d '{"hash": "22eac1c27af4be7b9d04da2ce1af111b", "title": "My Super App"}'
    ```

=== "HTTP GET"

    ```
    {{ extra.api_example_url }}/api/key/create?hash=a6aa75587e5c59c32d347da438505fc3&title=My+Super+App
    ```

#### response

```json
{
  "success": true,
  "value": {
    "hash": "c915157ac483e7319b0b257408bc04e1",
    "create_date": "2021-10-29 12:00:36",
    "title": "My Super App"
  }
}
```

#### errors

* 4 - User or API key not found or session ended. 
  If the user session (`hash` param) is invalid or a non-standard session is used (for example, another API key).
* 13 - Operation not permitted. If a call with subuser's session hash.
* 268 - Over quota. If 20 keys have already been created in the user's account.

***

### delete

Deletes API key.

This call is available only to the master user and only with a standard session
obtained using a login/password via [/user/auth](./user/index.md#auth).

#### parameters

| name  | description | type  | restrictions |
| :---- | :----       | :---- | :----        |
| hash | Master user's session hash. | String | Not empty. |
| key | The API key to delete. | String | Not empty.|

#### examples

=== "cURL"

    ```shell
    curl -X POST '{{ extra.api_example_url }}/api/key/delete' \
        -H 'Content-Type: application/json' \ 
        -d '{"hash": "22eac1c27af4be7b9d04da2ce1af111b", "key": "5063e191d734e87e17987953c7a9a086"}'
    ```

=== "HTTP GET"

    ```
    {{ extra.api_example_url }}/api/key/delete?hash=a6aa75587e5c59c32d347da438505fc3&key=5063e191d734e87e17987953c7a9a086
    ```

#### response

```json
{
  "success": true
}
```

#### errors

* 4 - User or API key not found or session ended.
  If the user session (`hash` param) is invalid or a non-standard session is used (for example, another API key).
* 13 - Operation not permitted. If a call with subuser's session hash.
* 201 – Not found in the database - if there is no specified API key in account.

***

### list

Gets all of API keys for an account.

#### parameters

| name | description | type |
| :----- | :-----  | :----- |
| hash | Master user's session hash. | String | Not empty. |

#### examples

=== "cURL"

    ```shell
    curl -X POST '{{ extra.api_example_url }}/api/key/list' \
        -H 'Content-Type: application/json' \ 
        -d '{"hash": "22eac1c27af4be7b9d04da2ce1af111b"}'
    ```

=== "HTTP GET"

    ```
    {{ extra.api_example_url }}/api/key/list?hash=a6aa75587e5c59c32d347da438505fc3
    ```

#### response

```json
{
  "list": [{
      "hash": "c915157ac483e7319b0b257408bc04e1",
      "create_date": "2021-10-29 12:00:36",
      "title": "My Super App"
  }, {
      "hash": "e3b7d1d727d21e064a190239b3403ee3",
      "create_date": "2021-11-19 16:06:03",
      "title": "AmoCRM integration"
  }],
  "success": true
}
```

#### errors

* 4 - User or API key not found or session ended.
  If the user session (`hash` param) is invalid or a non-standard session is used (for example, another API key).
* 13 - Operation not permitted. If a call with subuser's session hash.