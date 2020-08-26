# ts-step-functions-schedule-lambda-every-30sec

This is the example of running lambda function every 30 seconds by using step functions. As cloud watch event has the limitation of running scheduled lambda less than 60 seconds. With the step function, we schedule it to run every 60 seconds (the fastest with CloudWatch event) between 7am to 9pm AEST from Monday to Friday. Then, we run the same function twice with a waite step of 30 seconds.

This example only includes a mock code. However, it has all the set up to run integration and unit tests once you add the real code.

## Tools and Framework

- Serverless
- Webpack
- JavaScript
- Jenkins
- Mocha

## Get started

```bash
# Install dependencies
npm i

# Deploy
# nonprod
sls deploy --stage nonprod # if you have serverless installed globally (npm i -g serverless)
npm run deploy -- --stage nonprod

# prod
sls deploy --stage prod
npm run deploy -- --stage prod
```

Remove the function (this deletes the cloud formation)

```bash
sls remove --stage nonprod
npm run remove -- --stage nonprod
```

## Optimising Memory Allocation

Using AWS Lambda Power Tuning deployed to AWS Connect Account (see details [here](https://cbussuper-uat.atlassian.net/wiki/spaces/ES/pages/1172013219/1.+Optimising+Memory+Allocation)).

Note: do not run this with production function. It will mess with the data.

Input for the tuning

```json
{
  "lambdaARN": "arn:aws:lambda:ap-southeast-2:045770812520:function:nonprod-connect-metrics-ingestion",
  "powerValues": [
    128,
    256,
    512,
    1024,
    2048,
    3008
  ],
  "num": 10,
  "payload": "{\"id\":\"cdc73f9d-aea9-11e3-9d5a-835b769c0d9c\",\"detail-type\":\"Scheduled Event\",\"source\":\"aws.events\",\"account\":\"123456789012\",\"time\":\"1970-01-01T00:00:00Z\",\"region\":\"ap-southeast-2\",\"resources\":[\"arn:aws:events:ap-southeast-2:123456789012:rule/ExampleRule\"],\"detail\":{}}",
  "parallelInvocation": true,
  "strategy": "balanced"
}
```