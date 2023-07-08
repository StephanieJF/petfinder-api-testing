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
            <li>Incorrect token passed to <code>/animals</code></li>
            <li>Incorrect token passed to <code>/types</code></li>
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

<details>
  <summary>Authentication</summary>
    <br>
    <p>Test Steps</p>
    <ol><li>Send a request for an access token, passing in encrypted variables for <code>client_id</code> and <code>client_secret</code></li>
        <li>Capture the access token from the response body and store in an encrypted variable to be included in the header for subsequent requests</li>
    </ol>
    <p>Expected Results</p>
    <ul><li>Returned successful status code of <code>200</code></li></ul>

</details>

<details>
    <summary>Happy Path API Requests</summary>
      <br>
      <p>Base Call: <code>GET https://api.petfinder.com/v2</code> </p>
  
  <ul><li><details>
        <summary><code>GET /animals</code></summary>
        <p><em>Returns one "page" of animals (default of 20 animals per page)</em></p>
        <p>Expected Results</p>
        <ul><li>Returned successful status code of <code>200</code></li>
          <li>Response time is less than 2 seconds</li>
          <li>Returns one page with the default number of animals (20)</li>
          <li>Returns a JSON object containing an <code>"animals"</code> array of objects</li>
      </ul>
  <hr>
        <p>Query parameters: Location</p>
        <ul>
          <li>By City, State: <code>GET /animals?location={city}, {state}</code></li>
          <li>By Postal Code: <code>GET /animals?location={postal_code}</code></li>
        <ul><li>Expected Results:</li>
          <ul>
            <li>Accepts a string parameter</li>
            <li>Returned successful status code of <code>200</code></li>
                <li>Response time is less than 2 seconds</li>
                <li>Animals returned have distance property value less than the default 100 miles</li>
            </ul>
            </ul>
        </ul>
  </br>
      <p>Query parameters: Distance</p>
    <ul>
      <li>By Miles: <code>/animals?location={postal_code}&distance={miles}</code></li>
        <ul>
          <li>Expected Results:</li>
            <ul>
              <li>Accepts an integer, max: 500</li>
              <li>Returned successful status code of <code>200</code></li>
              <li>Response time is less than 2 seconds</li>
              <li>Animals returned have distance property value less than miles parameter</li>
            </ul>
        </ul>
    </ul>
  </details></li></ul>
  
  <ul><li><details>
        <summary><code>GET /types</code></summary>
        <p><em>Returns an array with every animal type</em></p>
        <p>Expected Results</p>
        <ul><li>Returned successful status code of <code>200</code></li>
          <li>Response time is less than 2 seconds</li>
          <li>Response includes every animal type in the database</li>
      </ul>
  </details></li></ul>

  <ul><li><details>
        <summary><code>GET /animals/{id}</code></summary>
        <p><em>Returns details on a specific animal based on an integer ID</em></p>
        <p>Expected Results</p>
        <ul><li>Accepts an integer</li>
          <li>Only succeeds if there is an ID match in the database</li>
          <li>Returned successful status code of <code>200</code></li>
          <li>Response time is less than 2 seconds</li>
          <li>Response contains an object</li>
          <li>Animal returned has an ID that matches the ID sent in the request</li>
      </ul>
  </details></li></ul>
  </details>

<details>
  <summary>Sad Path API Requests</summary>
    <br>
    
  <ul><li><details>
          <summary><code>401</code> Invalid Auth</summary>
    </br>
        <p>Scenarios Tested:</p>
            <ul>
                <li>No Access Token</li>
                <li>Invalid Auth Type (Basic auth with username and pw)</li>
                <li>Incorrect Token Input</li>
            </ul>
    </br>
        <p>Expected Results:</p>
            <ul>
                <li>Returned status code of <code>401</code></li>
                <li>Response message <code>"Access token invalid or expired"</code></li>
            </ul>
    </details></li></ul>
</details>
