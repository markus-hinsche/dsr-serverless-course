# Create & use lambda using aws-cli

```bash
cd example-4-hello-world-cli
```

1. Create an IAM Role for AWS Lambda, you need Admin user to do that:

```bash
aws cloudformation deploy \
    --template-file templates/iam-role.yaml \
    --stack-name slambda-iam-role \
    --region eu-central-1 \
    --capabilities CAPABILITY_NAMED_IAM
```

2. To get the IAM Role Arn, use:

```bash
ROLE_ARN=$(aws cloudformation describe-stacks --stack-name slambda-iam-role --query 'Stacks[0].Outputs[?OutputKey==`LambdaExecutionRoleArn`].OutputValue' --out text) && echo $ROLE_ARN
```

3. Create an archive by zipping your code:

```bash
zip -r my_lambda lambda_function.py
```

4. Create a lambda:

```bash
MY_NAME=$(whoami)
aws lambda create-function \
    --function-name $MY_NAME-function-from-cli \
    --runtime python3.9 \
    --handler lambda_function.lambda_handler \
    --role $ROLE_ARN \
    --zip-file fileb://my_lambda.zip
```

The expected output should look like:

```
{
    "FunctionName": "markus-function-from-cli",
    "FunctionArn": "arn:aws:lambda:eu-central-1:434405979992:function:markus-function-from-cli",
    "Runtime": "python3.9",
    "Role": "arn:aws:iam::434405979992:role/slambda-iam-role-LambdaExecutionRole-RE7ACGPZ3XSX",
    "Handler": "lambda_function.lambda_handler",
    "CodeSize": 344,
    "Description": "",
    "Timeout": 3,
    "MemorySize": 128,
    "LastModified": "2020-11-08T22:17:58.516+0000",
    "CodeSha256": "8vijcvNSB711jwkPFfPSqwSzzTNPS2eOqVrO3cBZoUA=",
    "Version": "$LATEST",
    "TracingConfig": {
        "Mode": "PassThrough"
    },
    "RevisionId": "613ec6b1-9b18-41a8-86b0-8561e399245a",
    "State": "Active",
    "LastUpdateStatus": "Successful"
}
```

5. Invoke the lambda and notice the `out` file in your local machine folder. It contains the logs:

```bash
aws lambda invoke \
    --function-name $MY_NAME-function-from-cli \
    out \
    --log-type Tail \
    --query 'LogResult' \
    --output text | base64 -d
```

The expected output should look like:

```
START RequestId: d8be8669-a0cc-4c31-9de2-edb7f58fa4be Version: $LATEST
inside the lambda function cli
[ERROR] KeyError: 'key1'
Traceback (most recent call last):
  File "/var/task/lambda_function.py", line 4, in lambda_handler
    print("value1 = ", event['key1'])END RequestId: d8be8669-a0cc-4c31-9de2-edb7f58fa4be
REPORT RequestId: d8be8669-a0cc-4c31-9de2-edb7f58fa4be	Duration: 18.99 ms	Billed Duration: 19 ms	Memory Size: 128 MB	Max Memory Used: 36 MB	Init Duration: 112.16 ms
```

6. Pass a payload when invoking so the functions receives inputs, and redeploy (Note that you have to repackage the lambda and update the code):

```bash
zip -r my_lambda lambda_function.py

aws lambda update-function-code \
    --function-name $MY_NAME-function-from-cli \
    --zip-file fileb://my_lambda.zip

aws lambda invoke \
    --function-name $MY_NAME-function-from-cli \
    --payload '{"key1": "value1 of key1"}' \
    out \
    --log-type Tail \
    --query 'LogResult' \
    --output text | base64 -d
```

The expected output should look like:

```
    START RequestId: 7c79341c-86ca-4066-b745-5975faedb446 Version: $LATEST
    inside the lambda function
    value1 =  value1 of key1
    END RequestId: 7c79341c-86ca-4066-b745-5975faedb446
    REPORT RequestId: 7c79341c-86ca-4066-b745-5975faedb446	Duration: 0.22 ms	Billed Duration: 100 ms	Memory Size: 128 MB	Max Memory Used: 50 MB	Init Duration: 0.89 ms
```

Note: for other OS, try using `Base64 -D`

7. Check it all in the AWS console.

### to update the function code, use:

```bash
zip -r my_lambda lambda_function.py && aws lambda update-function-code --function-name $MY_NAME-function-from-cli --zip-file fileb://my_lambda.zip
```

### to clean up, use:

```bash
aws lambda delete-function --function-name $MY_NAME-function-from-cli

aws cloudformation delete-stack --stack-name slambda-iam-role
```

## Takeaways:

- Infra as Code (Cloudformation)
- Usage of AWS CLI to create and update resources
