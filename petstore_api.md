# Petstore API

**Petstore API** is a set of operations that allows you to manage your own pet shop. It is easy to integrate and use, especially if you are a beginner and do not need complicated software.

With the Petstore API, you can manage such data as:

* The pets displayed in your store, their photos and purchase status.
* Users, whether it would be employees or customers.
* Store purchases like pets, inventory, etc.

Although the Petstore API includes a big variety of methods, in this article we will observe only a few of them to give you a brief explanation of how you can use the Petstore API in general.

## Usage

We will use methods containing pets database as an example.

Use a **Pet** object for operations with pets.

### Pet parameters

|Parameter|Type|Description|
|-----------|-----------|-----------|
|**id**|integer|Pet ID|
|**category**|object|Shop category for the pet|
|category.**id**|integer|Category ID|
|category.**name**|string|Category name|
|**name**<br>_required_|string|Pet name|
|**photoUrls**<br>_required_|array[string]|Pet photo URLs|
|**tags**|array[object]|Tags for the pet. You can add multiple tags|
|tags.**id**|integer|Tag ID|
|tags.**name**|string|Tag name|
|**status**|string|Pet status in the store<br>Enum: `available`, `pending`, `sold` |

#### Example

```json
{
  "id": 234234234,
  "category": {
    "id": 234234234,
    "name": "Dogs"
  },
  "name": "Cheems",
  "photoUrls": [
    "https://play-lh.googleusercontent.com/xlnwmXFvzc9Avfl1ppJVURc7f3WynHvlA749D1lPjT-_bxycZIj3mODkNV_GfIKOYJmG"
  ],
  "tags": [
    {
      "id": 3,
      "name": "dog"
    },
    {
      "id": 8,
      "name": "shiba"
    }
  ],
  "status": "available"
}
```

### Add pet to database

Use a `POST /pet` method to add the pet in your database. The request should contain the [pet object](#pet-object) as a request body.

#### Request example

```sh
curl -X 'POST' \
  'https://petstore.swagger.io/v2/pet' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "doggie",
  "photoUrls": [
    "url.com/my-url"
  ]
}'
```

#### Response types

|Code|Description|
|--------|------------|
|200|Success. The request body will be returned.|
|405|Invalid input. Check the required parameters.|

### Find pet by its ID

Use a `GET /pet/{petId}` method to find the pet in your database.

The pet ID is required in the request header.

#### Request example

```sh
curl -X 'GET' \
  'https://petstore.swagger.io/v2/pet/234234234' \
  -H 'accept: application/json'
```

#### Response types

|Code|Description|
|--------|------------|
|200|Success. The [pet object](#pet-object) will be returned.|
|400|Invalid pet ID supplied in the request.|
|404|Pet wasn't found.|

### Delete pet from database

Use a `DELETE /pet/{petId}` method to delete the pet from your database.

The pet ID is required in the request header. 

For the security purposes, you can specify an optional `api_key` parameter. Use `special-key` value to test this method.

#### Request example

```sh
curl -X 'DELETE' \
  'https://petstore.swagger.io/v2/pet/234234234' \
  -H 'accept: application/json' \
  -H 'api_key: special-key'
```

#### Response types

|Code|Description|
|--------|------------|
|200|Success. The pet ID will be returned.|
|400|Invalid pet ID supplied in the request.|
|404|Pet wasn't found.|

## Integration example

After you know how you can use the Petstore API, you need to build an integration with your software.

We will show a `GET /pet/findByStatus` method integration as an example, written in **Python**. This method allows you to get the pet list depending on its purchase status. We will use it to display the IDs and names of the first 10 pets in the `available` status.

It is required to import the Python [requests](https://requests.readthedocs.io/en/latest/) library in order to call our method:

```python
import requests
```

At first, specify the request URL.

```python
request_url = "https://petstore.swagger.io/v2/pet/findByStatus"
```

You will also need the pets purchase status in the request header.

```python
status = {"status": "available"}
```

The last step is to use `get` function in the `requests` library. Refer to request URL and status variables as arguments for the function. The status comes under the argument named `params`.

```python
pets_status_request = requests.get(request_url, params=status)
```

Save the response in `pets_array` variable in `.json`. The response will contain all [pet objects](#pet-object) with the `available` purchase status.

```python
pets_array = pets_status_request.json()
```

Take a "slice" of the first 10 values of the `pets_array`.

```python
pets_array = pets_array[:9]
```

Extract the pets IDs and names from your objects and save these values in a separate variable.

```python
pets_names_ids = ''

for item in pets_array:
    pets_names_ids += (str(item.get('id')) + ' ' + item.get('name') + '\n')
```

An expected output should look like this:

```
9223372036854576593 doggie
9223372036854576594 cheems
9223372036854576595 cat
9223372036854576596 emmanuel
9223372036854576597 meow
9223372036854576598 piggie
9223372036854576599 hamster
9223372036854576601 parrot
9223372036854576602 fish
```