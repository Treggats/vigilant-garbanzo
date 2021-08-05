---
layout: article
title: Laravel frontend testing with Cypress
date: 2021-01-21
categories: [laravel,frontend,testing]
---

Within testing on the frontend there are a few options

- Laravel Dusk
- jest
- Cypress
- Jasmin
- Selenium

Only the first is PHP based, the rest is JavaScript based.

The first is also heavily integrated with the Laravel framework. Hence the name.

For the project I'm working on I'm using not Dusk, but Cypress. The reason for this is that the app uses subdomains, I found that Dusk doesn't handle this well. I could be wrong here, please let me know.

Since Cypress is not tightly integrated with Laravel, I am using a package by Jeffrey Way `laracasts/cypress` which extends the Cypress commands with ajax calls to Laravel for different tasks. For example; create a model, refresh the database or to execute a artisan command.

## Setup
The package has one command and that is to create the boilerplate that is used by the tests.
This boilerplate include the basic Cypress files, and a file in which all the different commands are defined.

These are a their base ajax calls to the backend, and the package also provides the endpoints for these commands.

In order for Cypress to know which site to visit, you need to create `cypress.json` with the `baseUrl` property.

And if you create a `.env.cypress` file, then those values are going to be used in the test runs.
Note: be sure to mirror the environment variables from the phpunit config.

## Initial testcase
Writing the first test is pretty straight forward
1. In the integration folder, create a `.spec.js` file
2. In there you use `describe` or `context` to label the scenario that you are going to test
3. Use `it` to label the test itself

### Example
`login.spec.js`
```
describe(verify the login ability', () => {
    context('unable to login', () => {
        it('can not login without an account', () => {
            cy.visit('login')
                .get('#loginForm input[name=email]')
                .type('very secret password')

                .click('#loginButton')
                .get('body')
                .should('contain', 'hello')
        })
    }
} 
```

## Setting the stage
Sometimes, if not always, you want to do something before or after every test or scenario. Cypress has a set of commands for this purpose;
- before
- beforeEach
- after
- afterEach

These are similar to what phpunit has with `setUp` and `tearDown`.
Though I'm not sure if there is an equivalent to `cy.before`.
