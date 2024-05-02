# Alkemio - Quality Assurance

This document provides an overview of the quality assurance and testing activities that are carried out on the Alkemio platform.

# Process
 
- Perform functional/non-functional testing types on reviewed "github" issues
    - Depending on the "github" issue scope, testing is performed on different environments 
- Log defects, communicate  and retest them
- Perform regression testing as part of "github" issues testing
- Automate/maintain test cases on different levels
- Test data management
- Package dependency audit: regular review of reported vulnerabilities in packages dependent on and then address


# Testing types and levels

Alkemio consists of multiple services. The following test types are being applied among the different test levels:

## "Server" - testing types and levels:

First QA priority is the "Server" repository quality

- ### Functional testing: 
    - Unit testing<sup>*</sup>
    - [GraphQL API integration tests](https://github.com/alkem-io/server-api-tests/tree/develop/test/functional)
    - GraphQL Exploratory testing
        
- ### Non-functional testing:
    - [Performance testing](https://github.com/alkem-io/server-api-tests/tree/develop/test/non-functional/performance)
    - Security testing
         - [Authorization tests and accounts](https://github.com/alkem-io/server-api-tests/tree/develop/test/non-functional/auth)
              - admin@alkem.io
              - ecoverse.admin@alkem.io
    - [Database migrations](https://github.com/alkem-io/server/develop/README.md)

- ### Static testing:
    - Code review
    - Static code analyses tools:
        - Build Status:![Docker Image CI](https://github.com/alkem-io/server/workflows/Docker%20Image%20CI/badge.svg?branch=master)

        - Build Quality:[![BCH compliance](https://bettercodehub.com/edge/badge/Alkemio/Server?branch=develop)](https://bettercodehub.com/)
 
## "Client.Web" - testing types and levels:

Secondary QA priority is the "Client.Web" repository quality

- ### Functional testing: 
    - Component testing<sup>*</sup>
    - Integration testing<sup>*</sup>
    - E2E automation testing<sup>*</sup>
    - Exploratory testing
        
- ### Non-functional testing:
    - Performance testing<sup>*</sup>
    - Security testing<sup>*</sup>

- ### Static testing:
    - Code review
    - Static code analyses tools:
        - Build Status:![AKS dev CI/CD pipeline](https://github.com/alkem-io/Client.Web/workflows/AKS%20dev%20CI/CD%20pipeline/badge.svg?branch=develop)

        - Build Quality:[![BCH compliance](https://bettercodehub.com/edge/badge/Alkemio/Client.Web?branch=develop)](https://bettercodehub.com/)

# Reporting
 
- [Test cases](https://app.travis-ci.com/github/alkem-io/server-api-tests)

    - Number of test cases
    - Number of failed test cases per service

- Defects<sup>*</sup>

    - Number of defects<sup>*</sup>
    - Number of production environment defects by severity<sup>*</sup>
    - Number of development environment defects by severity<sup>*</sup>

# Environments
- Local - setup for [server](https://github.com/alkem-io/server) and [client-web](https://github.com/alkem-io/client-web)
- [Development](https://dev.alkem.io/) - auto-deployed after each merge to develop branch
- [Test](https://test.alkem.io/) - QA deploys from "github" repositories action, the branch version that is being tested
- [Acceptance](https://acc.alkem.io/) 
- [Production](https://hub.alkem.io/) 

# Tools
- [VS Code](https://code.visualstudio.com/) - IDE for automation test development
- [Jest](https://jestjs.io/) - javascript automation test framework
- [Jmeter](https://jmeter.apache.org/) - performance test tool 
- [Postman](https://www.postman.com/) - API testing tool
- [MySQL 8](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/) - query data and testing the systems integration
- [Populator](https://github.com/alkem-io/Populator) - test data population
- Test management system - for the moment, the manual testing is tracked within "github" issues
    - [KIWI TCMS](https://kiwitcms.org/features/) is being evaluated for manual test case management

**NOTE**

Test terminology meaning is based on: ["ISTQB" syllabus](https://www.istqb.org/downloads/send/69-2019-advanced-level-test-analyst/303-advanced-level-syllabus-2019-1-test-analyst.html)

<sup>*</sup>to be defined/implemented

# Release - QA checklist

### Pre-deployment

- [ ] Nightly test build is passing    [![Build Status](https://app.travis-ci.com/alkem-io/server-api-tests.svg?branch=develop)](https://app.travis-ci.com/alkem-io/server-api-tests)
- [ ] Server CI: passing  [![Build Status](https://app.travis-ci.com/alkem-io/server.svg?branch=develop)](https://app.travis-ci.com/alkem-io/server)
- [ ] Client-web CI: passing [![Build Status](https://app.travis-ci.com/alkem-io/client-web.svg?branch=develop)](https://app.travis-ci.com/alkem-io/client-web)
- [ ] New features/bugs are tested manually and/or automated
  - [ ] Feature/Bug "x"
  - [ ] Feature/Bug "y"
  - [ ] Feature/Bug "y"
- [ ] Update scripts: tested
- [ ] Migration scripts: tested
- [ ] Performance/Security analyses, if required


### Post-deployment

- [x] System smoke test
  - [ ] Sign in/out
  - [ ] User registration
  - [ ] User profile update
  - [ ] User access to available resources
    - [ ] Space page
    - [ ] Subspace page
    - [ ] Subspace page
    - [ ] Search page
    - [ ] Admin page
      - [ ] Spaces
      - [ ] Users
      - [ ] Organizations
      - [ ] Authorization
- [ ] Update scripts: verify no data loss/coruption
- [ ] Migration scripts: verify no data loss/coruption
- [ ] Logs are error free
- [ ] Performance/Security analyses, if required
