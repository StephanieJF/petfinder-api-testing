# RESTful API testing with CI using GitHub Actions

A Postman collection created to test the [Petfinder.com](https://www.petfinder.com) RESTful API.

This project was created to practice using the Postman GUI for API testing and authentication methods, as well as exporting and automating the collection to run with the Newman CLI, and generating HTML reports of test outcomes via the Newman Reporter in a continuous integration fashion with GitHub Actions.

## Overview

#### Project Highlights:

- Authentication using OAuth 2.0
- Automated continuous integration (CI) set to run the test collection with Newman any time an update is made to the `main` repository branch via GitHub Actions
- Generates an HTML report of the test outcomes at runtime and exports the report to a folder called Artifacts

#### Links:

- [Petfinder API Documentation](https://www.petfinder.com/developers/v2/docs/)
- [Newman Runner](https://www.npmjs.com/package/newman)
- [Newman Reporter](https://www.npmjs.com/package/newman-reporter-htmlextra)

## Test Details

<p>Base Url: <code>https://api.petfinder.com/v2</code></p>

<details>
  <summary>Authentication</summary>

### Get token

```http
POST /oauth2/token
```

<strong>Steps</strong>

<ol><li>Send a request for an access token, passing in encrypted variables for <code>client_id</code> and <code>client_secret</code></li>
<li>Capture the access token from the response body and store in an encrypted variable to be included in the header to authorize subsequent requests</li></ol>

<strong>Expected Results</strong>

<ul><li>Returned successful status code of <code>200</code></li></ul>

</details>

<details>
    <summary>Happy Path API Requests</summary>
    
### Get all animals
<p><em>Returns one "page" of animals (default of 20 animals per page)</em></p>

```http
GET /animals
```

<strong>Expected Results</strong>

<ul>
    <li>Returns successful status code of <code>200</code></li>
    <li>Response time is less than 2 seconds</li>
    <li>Returns one page with the default number of animals (20)</li>
    <li>Returns a JSON object containing an <code>"animals"</code> array of objects</li>
</ul>

---

### Get animals by location

<p><em>Accepts a string query parameter for location and returns animals within the default 100 mile proximity</em></p>

```http
GET /animals?location={city, state}
```

```http
GET /animals?location={postal_code}
```

<strong>Expected Results</strong>

<ul>
    <li>Accepts a string parameter</li>
    <li>Returns successful status code of <code>200</code></li>
    <li>Response time is less than 2 seconds</li>
    <li>Animals returned have distance property value less than the default 100 miles</li>
</ul>

---

### Get animals by distance

<p><em>Accepts an integer query parameter for distance in miles and returns animals within the specified proximity of the set location</em></p>

```http
GET /animals?location={postal_code}&distance={miles}
```

<strong>Expected Results</strong>

<ul>
    <li>Accepts an integer, max: 500</li>
    <li>Returns successful status code of <code>200</code></li>
    <li>Response time is less than 2 seconds</li>
    <li>Animal objects returned have <code>distance</code> property values less than miles parameter requested</li>
</ul>

---

### Get all animal types

<p><em>Returns an array with every animal type</em></p>

```http
GET /types
```

<strong>Expected Results</strong>

<ul>
    <li>Returns successful status code of <code>200</code></li>
    <li>Response time is less than 2 seconds</li>
    <li>Response includes every animal type in the database</li>
</ul>

---

### Get animal by ID

<p><em>Returns details on a specific animal based on an integer ID</em></p>

```http
GET /animals/{id}
```

<strong>Expected Results</strong>

<ul>
    <li>Accepts an integer</li>
    <li>Only succeeds if there is an ID match in the database</li>
    <li>Returns successful status code of <code>200</code></li>
    <li>Response time is less than 2 seconds</li>
    <li>Response contains an object</li>
    <li>Animal returned has an ID that matches the ID sent in the request</li>
</ul>

</details>

<details>
    <summary>Sad Path API Requests</summary>
        
### Invalid token passed to `/animals`
<p><em>Attempts to make a request with an invalid token</em></p>

```http
GET /animals
```

```http
header: 'Authorization: Bearer abc123'
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>401</code> Unauthorized</li>
    <li>Returns message with <code>"Access token invalid or expired"</code></li>
</ul>

---

### Invalid token passed to `/types`

<p><em>Attempts to make a request with an invalid token</em></p>

```http
GET /types
```

```http
header: 'Authorization: Bearer abc---123'
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>401</code> Unauthorized</li>
    <li>Returns message with <code>"Access token invalid or expired"</code></li>
</ul>

---

### Missing token

<p><em>Attempts to make a request without a token</em></p>

```http
GET /animals
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>401</code> Unauthorized</li>
    <li>Returns message with <code>"Access token invalid or expired"</code></li>
</ul>

---

### Incorrect authorization type

<p><em>Attempts to make a request using Basic Auth and passing a Username and Password</em></p>

```http
GET /animals
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>401</code> Unauthorized</li>
    <li>Returns message with <code>"Access token invalid or expired"</code></li>
</ul>

---

### Invalid endpoint `/animal`

<p><em>Attempts to make a request to an invalid endpoint</em></p>

```http
GET /animal
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>404</code></li>
    <li>Returns status title <code>"Not Found"</code></li>
    <li>Response detail contains <code>"No route found"</code></li>
</ul>

---

### Invalid endpoint `/animals/types`

<p><em>Attempts to make a request to an invalid endpoint</em></p>

```http
GET /animals/types
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>404</code></li>
    <li>Returns status title <code>"Not Found"</code></li>
    <li>Response detail contains <code>"No route found"</code></li>
</ul>

---

### Get animal by ID, with ID as a string

<p><em>Attempts to request animal by ID, where ID is a string</em></p>

```http
GET /animals/abcdefg
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>404</code></li>
    <li>Returns status title <code>"Not Found"</code></li>
    <li>Returns response detail <code>"Not Found"</code></li>
</ul>

---

### Get animal by ID that does not exist

<p><em>Attempts to request animal by ID, where ID does not exist</em></p>

```http
GET /animals/11111111
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>404</code></li>
    <li>Returns status title <code>"Not Found"</code></li>
    <li>Returns response detail <code>"Not Found"</code></li>
</ul>

---

### Get animal by ID containing special characters

<p><em>Attempts to request animal by ID, where ID contains special characters</em></p>

```http
GET /animals/65289840!
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>404</code></li>
    <li>Returns status title <code>"Not Found"</code></li>
    <li>Returns response detail <code>"Not Found"</code></li>
</ul>

<strong>Actual Results</strong>

<ul>
    <li>Returns status code of <code>200</code></li>
    <li>Returns an animal with ID <code>65289840</code></li>
</ul>

---

### Get animals by incomplete location parameter

<p><em>Attempts to request animals by city without including the state parameter</em></p>

```http
GET /animals?location=denver
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>400</code> bad request</li>
    <li>Returns response detail <code>"The request contains invalid parameters."</code></li>
    <li>Returns error type <code>"ERR-00002"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the <code>"query"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the parameter named, <code>"location"</code></li>
    <li>Response with <code>invalid-params</code> array indicating a parameter error reason of <code>"Could not determine location."</code></li>
</ul>

---

### Get animals by state parameter with special characters

<p><em>Attempts to request animals by state with special characters</em></p>

```http
GET /animals?location=washington*
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>400</code> bad request</li>
    <li>Returns response detail <code>"The request contains invalid parameters."</code></li>
    <li>Returns error type <code>"ERR-00002"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the <code>"query"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the parameter named, <code>"location"</code></li>
    <li>Response with <code>invalid-params</code> array indicating a parameter error reason of <code>"Could not determine location."</code></li>
</ul>

---

### Get animals by postal code parameter with special characters

<p><em>Attempts to request animals by postal code with special characters</em></p>

```http
GET /animals?location=34787!
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>400</code> bad request</li>
    <li>Returns response detail <code>"The request contains invalid parameters."</code></li>
    <li>Returns error type <code>"ERR-00002"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the <code>"query"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the parameter named, <code>"location"</code></li>
    <li>Response with <code>invalid-params</code> array indicating a parameter error reason of <code>"Could not determine location."</code></li>
</ul>

---

### Get animals by distance miles, with distance as a string

<p><em>Attempts to request animals by distance from a location in miles, where distance is a string</em></p>

```http
GET /animals?location=34787&distance=twenty
```

<strong>Expected Results</strong>

<ul>
    <li>Returns status code of <code>400</code> bad request</li>
    <li>Returns response detail <code>"The request contains invalid parameters."</code></li>
    <li>Returns error type <code>"ERR-00002"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the <code>"query"</code></li>
    <li>Response with <code>invalid-params</code> array indicating an error in the parameter named, <code>"distance"</code></li>
    <li>Response with <code>invalid-params</code> array indicating a parameter error reason of <code>"This value should be a valid number."</code></li>
</ul>

</details>
