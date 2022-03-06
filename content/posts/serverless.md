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
