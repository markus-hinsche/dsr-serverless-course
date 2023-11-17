# Text to speech example, using boto3 (Python SDK)

First, manually create a bucket with a unique name in AWS console's S3 section.
The access should be public, ACL should be enabled.

```bash
cd example-6-polly
```

To create a new lambda, the role and the s3 bucket:

```bash
. scripts/setup-polly.sh example-6-polly-fct
```

![Screenshot](architecture/setup-lambda-polly.png)

To deploy a lambda and invoke it:

```bash
. scripts/deploy.sh example-6-polly-fct '{"bucket-name": "markus-dsr-polly-foobar"}'
```

![Screenshot](architecture/invoke-lambda-polly.png)

Copy the URL to your browser and download the file!

Note: Don't forget to clean your stacks by executing:

```bash
. scripts/clean.sh example-6-polly-fct markus-dsr-polly-foobar
```
