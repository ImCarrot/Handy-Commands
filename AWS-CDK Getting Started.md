# CDK Getting Started

## Structure of CDK Project

![Source: Pluralsight](https://user-images.githubusercontent.com/16665638/101532241-450b4980-39ba-11eb-99b8-fa0971d95596.png)

### App: 
- is the root of the context tree for a `CDK` project.
- is a group of `stacks` used to manage an application deployed to the cloud.

### Stack
- is the unit of deployment in a `CDK`.
- corresponds to a `Stack` in `CloudFormation`.
- can have limited number of resources in it.
- Every stack has an environment.

### Environment
- the combination of the `aws account` and `aws region`.

### Construct
- logical grouping of one or more `Resources`.
- building blocks within a `CDK`.
- can be programmatically customized.
- enable reuse within organizations.

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
- You should always pin your `CDK versions` to exactly one version and not use the caret `^`. This is because all dependencies for `@aws` must be on the same verison. If either of the version conflicts, you might just end up with an non intuitive error message. 

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

### View Created Apps
```
cdk list
```

### Deploy the application
```
cdk deploy <NAME_OF_THE_STACK_FROM_ABOVE_COMMAND> --profile <NAME_OF_THE_PROFILE> 
```
### Check your export on the terminal
```
aws --profile <NAME_OF_THE_PROFILE> --region <NAME_OF_REGION> cloudformation list-exports
```

### Checking for changes
once changes are done, we need to check all the changes before we can deploy. For that run the below command:
```
cdk diff
```

### Deleting a Stack
```
cdk destroy <NAME_OF_STACK>
```
