# CDK Getting Started

## Install AWS-CDK via npm (make sure you have node installed)
```
npm install -g aws-cdk
```

## Check cdk version
```
cdk --version
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

### View Created Stacks
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

