**WIP**
# RESTful API testing with CI using GitHub Actions

A Postman collection created to test the [Petfinder.com](https://www.petfinder.com) RESTful API.  


This project was created to practice using the Postman GUI for API testing and authentication methods, as well as exporting and automating the collection to run with the Newman CLI, and generating HTML reports of test outcomes via the Newman Reporter in a continuous integration fashion with GitHub Actions.

## Overview

Links:

- [Petfinder API Documentation](https://www.petfinder.com/developers/v2/docs/)
- [Newman Runner](https://www.npmjs.com/package/newman)
- [Newman Reporter](https://www.npmjs.com/package/newman-reporter-htmlextra)

Project Highlights:

- Authentication using OAuth 2.0
- Automated continuous integration (CI) set to run the test collection with Newman any time an update is made to the `main` repository branch via GitHub Actions
- Generates an HTML report of the test outcomes at runtime and exports the report to a folder called Artifacts   



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
  

