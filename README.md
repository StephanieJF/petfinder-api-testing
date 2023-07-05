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
