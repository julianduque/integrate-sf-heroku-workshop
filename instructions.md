# Instructions

1. Clone the following Node.js project from GitHub: [https://github.com/heroku-examples/lumina-solar-api](https://github.com/heroku-examples/lumina-solar-api)
2. Create an Heroku application, provision a Heroku Postgres add-on, and deploy the Node.js API project using Git

> 💡 Tip: Use your username as prefix for app name to make it unique
>
> `jduque-lumina-solar`

> 💡 Tip: For local development you can fetch the database configuration by running:
>
> `heroku config --shell > .env`

3. Import the database schema using by running

```
npm run setup
```

4. Seed the database using

```
node data/seed.js
```

5. Verify the Node.js API is working by running: `heroku open`
6. On your Salesforce org, create an External Credential with a Named Principal and then a Named Credential for the Node.js API project
7. Create a Permission Set allowing the Named Principal
8. Create an External Service that uses the Named Credential and fetch the api spec using the relative url:

> eg: [https://jduque-lumina-api-08992d919b13.herokuapp.com/api-docs/json](https://jduque-lumina-api-08992d919b13.herokuapp.com/api-docs/json)

9. On your Salesforce org, create a Flow or Apex Class that executes the External Service and displays the systems data using the `getApiSystems` method.
