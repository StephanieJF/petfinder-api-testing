**WIP**

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

## Collection Structure

<ul>
    <li>Authentication
    <ul>
        <li>Get token</li>
    </ul>
</li>
    <li>Happy paths
    <ul>
        <li>Get all animals
        <ul>
        <li>Get animals by location
            <ul>
            <li>City, state</li>
            <li>Postal code</li>
            </ul>
        </li>
        <li>Get animals by distance from location</li>
        </ul>
        </li>
        <li>Get all animal types</li>
        <li>Get animal by ID</li>
    </ul>
    </li>
    <li>Sad paths
        <ul>
        <li>Invalid authorization
            <ul>
            <li>Invalid token passed to <code>/animals</code></li>
            <li>Invalid token passed to <code>/types</code></li>
            <li>Missing token</li>
            <li>Incorrect authorization type</li>
            </ul>
        </li>
        <li>Invalid endpoint
            <ul>
            <li>Invalid endpoint <code>/animal</code></li>
            <li>Invalid endpoint <code>/animals/types</code></li>
            </ul>
        </li>
        <li>Invalid parameters
            <ul>
            <li>Invalid IDs for get animal by ID
            <ul>
                <li>ID as a string</li>
                <li>ID with no database match</li>
                <li>ID with special characters</li>
            </ul>
            </li>
            <li>Invalid query parameters for location and distance
            <ul>
                <li>Location by city with empty state</li>
                <li>Location by state with special characters</li>
                <li>Location by postal code with special characters</li>
                <li>Distance parameter as a string</li>
            </ul>
            </li>
            </ul>
        </li>
        </ul>
    </li>
</ul>

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
    <li>Returns status code of <code>401</code> unauthorized</li>
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
    <li>Returns status code of <code>401</code> unauthorized</li>
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
    <li>Returns status code of <code>401</code> unauthorized</li>
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
    <li>Returns status code of <code>401</code> unauthorized</li>
    <li>Returns message with <code>"Access token invalid or expired"</code></li>
</ul>

</details>
