# Install dependencies

<https://docs.aws.amazon.com/lambda/latest/dg/python-package.html>

```bash
python -m venv my_virtual_env
source ./my_virtual_env/bin/activate
pip install munch
cd my_virtual_env/lib/python3.9/site-packages
zip -r ../../../../my_deployment_package.zip .
cd -
zip my_deployment_package.zip lambda_function.py
. scripts/deploy.sh example-7-dependencies '{"key1": "my-value"}'
```
