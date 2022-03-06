---
title: "Serverless"
date: 2022-03-06T18:16:54+04:00
draft: false
---

Recently I got a chance to work on a ETL pipeline that import users data from another company into our platform. they had a legacy Microsoft SQL Server running from 2009 !

the propose to send the user data with Azure Data Pipeline. I suggested to implement a webhook and send it over HTTPS but thier team doesn't seem to willing to do that so :(

So basically we need to pull the data in a time interval (1 min) in order to find out is there new user record to import or not. 

I choose to implelemnt this using [Serverless Framework](https://www.serverless.com/) 

Here I want to show how we can implement a aws step function using serverless framework.

so this is the senario:
we are going to create a pipeline to get the user ETH wallet address and get the list of ERC721 and ERC20 token transction and more!

## install serverless framework and packages

first we need to install serverless framework:

`npm install -g serverless`

serverless framework has a alias `sls` also a good cli which help you to bootstrap your setup using pre-defined templates. here we are going to use simple aws nodejs function:

`sls create -t aws-nodejs`

this command will create `handler.js` which contain our functions and also `serverless.yml` that contain all our configuration for this stack.


In order to use aws step functions we need to install aws step function pluging for serverless:

`npm install --save-dev serverless-step-functions`

then we need to add this pluging in our `serverless.yml` file:
``` 
plugins:
  - serverless-step-functions
  ```

now we can define our state machine as follow:

```


service: serverless-step-function-example

frameworkVersion: '3'

provider:
  name: aws
  runtime: nodejs12.x
  region: us-east-2

plugins:
  - serverless-step-functions

functions:
  checkWallet:
    handler: handler.checkWallet
  getNormalTransactions:
    handler: handler.getNormalTransactions

stepFunctions:
  stateMachines:
    processWalletFlow:
      name: processWalletFlow
      definition:
        StartAt: checkWallet
        States:
          checkWallet:
            Type: Task
            Resource:
              Fn::GetAtt: [checkWallet, Arn]
            Next: getNormalTransactions
          getNormalTransactions:
            Type: Task
            Resource:
              Fn::GetAtt: [getNormalTransactions, Arn]
            End: true

```
here we defined our `service` name. we have a `function` block that we will use as reference in the stateMachine definition. our step function name is `processWallet` that contain two state for now. its start at `checkWallet` state/function that is referenced to the function above and its Type is Task and the Next state is the `getNormalTransactions` which we mentioned that this is the last state for now. so lets define these functions in our `handler.js` file:

```
module.exports.checkWallet = async(event) =>{
return 'noramal transactions'
}

module.exports.getNormalTransactions = async(event) =>{
  return 'normal transactions'
}
```

now we can deploy our function to the aws. make sure you have installed `aws cli` and configured the credentials in the `~/.aws/credentials` so simply execute `sls deploy` and it will trigger the aws CloudFormation and will create the desired stacks.

this is the first part you can read more about this on the documentations below:

[serverless](https://www.serverless.com/category/guides-and-tutorials)


[serverless step functions plugin](https://www.serverless.com/category/guides-and-tutorials)

[github repo](https://github.com/amiiy/serverless-step-function-example)