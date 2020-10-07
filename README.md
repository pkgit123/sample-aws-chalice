# sample-aws-chalice
Sample repo to explore AWS Chalice, serverless with Python



To check exist AWS-CLI credentials:

```
$ aws iam get-user
```



To install Chalice, we’ll first create and activate a virtual environment in python3.7:

```
$ python3 --version
Python 3.7.3
$ python3 -m venv venv37
$ . venv37/bin/activate
```

Next we’ll install Chalice using `pip`:

```
$ python3 -m pip install chalice
```

You can verify you have chalice installed by running:

```
$ chalice --help
Usage: chalice [OPTIONS] COMMAND [ARGS]...
...
```

The next thing we’ll do is use the `chalice` command to create a new project:

```
$ chalice new-project helloworld
```

This will create a `helloworld` directory. Cd into this directory. You’ll see several files have been created for you:

```
$ cd helloworld
$ ls -la
drwxr-xr-x   .chalice
-rw-r--r--   app.py
-rw-r--r--   requirements.txt
```

Let’s deploy this app. Make sure you’re in the `helloworld` directory and run `chalice deploy`:

```
$ chalice deploy
Creating deployment package.
Creating IAM role: helloworld-dev
Creating lambda function: helloworld-dev
Creating Rest API
Resources deployed:
  - Lambda ARN: arn:aws:lambda:us-west-2:12345:function:helloworld-dev
  - Rest API URL: https://abcd.execute-api.us-west-2.amazonaws.com/api/
```

You now have an API up and running using API Gateway and Lambda:

```
$ curl https://qxea58oupc.execute-api.us-west-2.amazonaws.com/api/
{"hello": "world"}
```

Try making a change to the returned dictionary from the `index()` function. You can then redeploy your changes by running `chalice deploy`.

If you're done experimenting with Chalice and you'd like to cleanup, you can use the `chalice delete` command, and Chalice will delete all the resources it created when running the `chalice deploy` command.

```
$ chalice delete
Deleting Rest API: abcd4kwyl4
Deleting function aws:arn:lambda:region:123456789:helloworld-dev
Deleting IAM Role helloworld-dev
```



Our application so far has a single view that allows you to make an HTTP GET request to `/`. Now let’s suppose we want to capture parts of the URI:

```python
from chalice import Chalice

app = Chalice(app_name='helloworld')

CITIES_TO_STATE = {
    'seattle': 'WA',
    'portland': 'OR',
}


@app.route('/')
def index():
    return {'hello': 'world'}

@app.route('/cities/{city}')
def state_of_city(city):
    return {'state': CITIES_TO_STATE[city]}
```



So far, our examples have only allowed GET requests. It’s actually possible to support additional HTTP methods. Here’s an example of a view function that supports PUT:

```python
@app.route('/resource/{value}', methods=['PUT'])
def put_test(value):
    return {"value": value}
```

We can test this method using the `http` command.  Note the examples below use the `http` command from the `httpie` package. You can install this using `pip install httpie`:

```
$ http PUT https://endpoint/api/resource/foo
HTTP/1.1 200 OK

{
    "value": "foo"
}
```





### References:

* https://github.com/aws/chalice
* https://aws.github.io/chalice/
* https://aws.github.io/chalice/quickstart.html
* https://aws.github.io/chalice/tutorials/index.html
* https://aws.github.io/chalice/tutorials/basicrestapi.html