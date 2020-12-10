# CDK Getting Started

## Structure of CDK Project

![image](https://user-images.githubusercontent.com/16665638/101538466-265d8080-39c3-11eb-8325-b63d9013a0a6.png)

### App: 
- is the root of the context tree for a `CDK` project.
- is a group of `stacks` used to manage an application deployed to the cloud.

### Stack
- is the unit of deployment in a `CDK`.
- corresponds to a `Stack` in `CloudFormation`.
- can have limited number of resources in it. (200 Resources per `stack` and 200 `stacks` per `region`)
- Every stack has an environment.

### Environment
- the combination of the `aws account` and `aws region`.

### Construct
- logical grouping of one or more `Resources`.
- building blocks within a `CDK`.
- can be programmatically customized.
- enable reuse within organizations.

#### L0 Construct (a.k.a CloudFormation Object)
- in the `AWS CDK Docs` you'll find them listed as `CFN-Resources`.
- a direct map to what's present in `CloudFormation`.
- e.g. properties of a `API gateway` that you would define in `CloudFormation`.

#### L1 Construct
- in the `AWS CDK Docs` you'll find them listed as `CDK-Constructs`.
- written specifically for `CDK`.
- has a lot of default settings to enable you to run a particular infrastructure with default settings.

> Not all services have L2 Constructs. According to `AWS CDK Docs`, you'll find a lot of services have the `L2 Construct` marked as `Experimental` (meaning you can experience breaking changes with new versions) while you may not find `L2 Constructs` at all.

#### L2 Construct
- a collection of L0 and L1 Constructs.
- things that you would build.
- e.g. `vpc` would need `subnets` so a you could make a well architected `vpc` that would encapsulate `subnets`. 

#### L3 Construct (a.k.a Solution constructs or Patterns)
- built by amazon and open source contributors.
- groups many logical objects to easy out building and coding.
- Checkout *(by amazon)* [AWS Solutions Constructs Patterns](https://aws.amazon.com/solutions/constructs/patterns)
- Checkout *(open-source)* [AWS CDK Patterns](https://cdkpatterns.com/)

### Resource
- an infrastructure created via the CDK
- has an `identifier`
- maps to a `Resource` in `CloudFormation`.

## CDK Workflow

```
   +--------------------+      +--------------------+      +--------------------------+      +------------------------+
   |         Init       |      |      Bootstrap     |      |         Synth            |      |         Deploy         |
   | Project is created |      | Creates nedded AWS |      | Generates Cloudformation |      | Templates are launched |
   | using command line | ---> | resources for CDK  | ---> | templates from code      | ---> | by Cloudformation      |
   | command line tool  |      | environment.       |      |                          |      |                        |
   +--------------------+      +--------------------+      +--------------------------+      +------------------------+
                                                                       /|\                              |
                                                                        |                               |
                                                                        |                               |
                                                                        |                              \|/
                                                          +--------------------------+      +-------------------------+
                                                          |          Diff            |      |         Update          |
                                                          | Updates against deployed | <--- | CDK project is updated  |
                                                          | stack are identified     |      | with new infrastructure |
                                                          +--------------------------+      +-------------------------+
```

## Some useful advice
- You should always pin your `CDK versions` to exactly one version and not use the caret `^`. This is because all dependencies for `@aws` must be on the same verison. If either of the version conflicts, you might just end up with a non intuitive error message. 
- Divide your `App` into `stacks`. For example, if your application contains `S3`, `API Gateway`, `Lambda`, `DynamoDB` and `AmazonRDS`, then you would have 4 stacks, 
   - data: that'll contain `dynamodb` and `AmazonRDS`
   - storage: that'll contain `S3 Buckets` and `S3 Deployments`
   - serverless: that'll contain your `lambdas`
   - api: that'll contain your `API Gateway`
   - security: would have it's own stack containing `roles` and `policies`.
- To code multiple stacks, make a folder called `stacks` in your `lib` directory and then make sub folders for each stack. For example, for your `database-stack.ts` stack file, the path your be: `<PROJECT_BASE_PATH>/lib/stacks/database/database-stack.ts`

## Prerequisites
Even if you aren't using Typescript for your `CDK`, you do need `Node`, `npm` and `aws-cli` installed on your system `CDK` to work. You can use any other alternatives of `npm` if you're already familiar with it. It's also recommended that you use `aws-cli v2`.

Ensure you're all setup by running the below commands. *(I've attached the result of those commands too for reference)*

```
carrot@homesystem ~ % node -v
v14.15.1
carrot@homesystem ~ % npm -v
6.14.8
carrot@homesystem ~ % aws --version
aws-cli/2.1.6 Python/3.7.4 Darwin/19.6.0 exe/x86_64 prompt/off
```

## Install AWS-CDK via npm
```
npm install -g aws-cdk
```

The `-g` flag is to install `aws-cdk` globally for all projects and not for a specific project.

## Check cdk version
```
carrot@homesystem ~ % cdk --version
1.76.0 (build c207717)
```

## Starting a new cdk project
To start up a new project, we need to pick a language, for the sake of demo we'll pick `Typescript` but we can also pick other languages like `python`, `java`, `javascript`, `c#`. Refer on hello world sample [here](https://docs.aws.amazon.com/cdk/latest/guide/hello_world.html).

### Make a new directory for project files
```
mkdir <project_name> 
```

### Navigate to the created directory
```
cd <project_name> 
```

### Initialise cdk into the directory
```
cdk init app --language=typescript
```

the base template for the above command is:
```
cdk init TEMPLATE --language LANGUAGE
```


If you don't want to create a `stack` but are trying to create a sharable `Construct Library` instead, you can fire up the below command to skip out all the extra stuff
```
cdk init --language=typescript lib
```

### Bootstrap the AWS Account
```
cdk bootstrap
```

> You would have to run this command once per AWS environment.

This command would install a `Stack` with some `CDK Resources` that are needed to manage the `Stacks` within that environment.

### Lookup CDK docs for more information on constructs and AWS Services to wire up
```
cdk docs
```

### Install necessary packages for the aws services you want to use (e.g. for S3)
```
npm install @aws-cdk/aws-s3
```

### Bootstrap your cdk to the online AWS account
```
cdk bootstrap --profile <NAME_OF_THE_PROFILE>
```
The `--profile` is a key to provide a specific `named profile` that holds the keys to your AWS account. If you haven’t setup a profile you can always set up your aws account using `aws-cli` command `aws configure`

### Generate Cloudformation templates for this Stack
```
mkdir templates
cdk synth --output=./templates
```

> `--output` is optional if you don't want to output to a separate directory. Not passing this flag would print out the generated template on the `console`.

### View Created Stacks in the CDK application
```
cdk list
```

### Deploy the application
```
cdk deploy <NAME_OF_THE_STACK_FROM_ABOVE_COMMAND> --profile <NAME_OF_THE_PROFILE> 
```

> `--profile` is optional in case you want to run via the default credentials that you used for `aws configure`.

you can also deploy multiple stacks using their names:
```
cdk deploy <NAME_OF_THE_STACK_1> <NAME_OF_THE_STACK_2> 
```

or you can just use `*` for all
```
cdk deploy `*`
```

### To troubleshoot/check your stack
If something is not working the way it should or its your fist `CDK` setup and you want to check things in your `stack`

```
cdk docktor
```

### Check your export on the terminal
```
aws --profile <NAME_OF_THE_PROFILE> --region <NAME_OF_REGION> cloudformation list-exports
```

> `--profile` and `--region` are optional in case you want to run via the defaults that you used for `aws configure`.

### Checking for changes
once changes are done, we need to check all the changes before we can deploy. For that run the below command:
```
cdk diff
```

### Deleting a Stack
```
cdk destroy <NAME_OF_STACK>
```

## Testing
- Most popular library is `jest` but only usable with `Typescript` but that doesn't mean it can't test `constructs` written in python.

### Resolving Library Confusion

The online `CDK` docs & workshops use the deeper `jest` library and use `expect` while the `cdk init` would generate test files from the `@aws-cdk/assert` library and in that, `expect` is aliased as `expectCDK`. Both can be used side by side though.

```
// from @aws-cdk/assert/jest
expect(stack).toHaveResource('AWS::S3::Bucket');

// from @aws-cdk/assert
expectCDK(stack).to(haveResource('AWS::S3::Bucket'));
```

The `jest` library creates these functions as `extenders` to the base `assert` library.

### Snapshot Tests
- Tests the `synthesized` cloudformation assembly against a previous version. 
- Useful for refactoring, when you want the final outcome to be the same for before and after code.
- If your resource names are dynamic *(for instance your `S3` bucket is named dynamically)*, your `cloudformation` json would be different everytime we run a snapshot test. Hence the snapshot test would fail everytime.

### Fine-grained assertions
- uses `assertions` through `expect` statements.
- used to test for expected properties or characteristics of resources using the `.toHaveResource()` method of the `expect` class.
- verifies one part of the `construct's` behavior
- to build assetions, you'll need reference to `CloudFormation` properties. To lookup the property nomenclature refer to [AWS resource and property types reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html).

### Validation tests
- validates a construct does something that we expect it to do in different situations. e.g. property of a resource throws an error when a condition is not met.
- generally check using the `toThrowError()` method

## References and Read More
- [AWS CDK Workshop](https://cdkworkshop.com/)
- [CDK Hello World](https://docs.aws.amazon.com/cdk/latest/guide/hello_world.html)
- [CDK Samples](https://github.com/aws-samples/aws-cdk-examples)
- [AWS Solutions Constructs Patterns](https://aws.amazon.com/solutions/constructs/patterns)
- [AWS CDK Patterns](https://cdkpatterns.com/)
- [AWS Docs on Testing](https://docs.aws.amazon.com/cdk/latest/guide/testing.html)