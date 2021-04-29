# Open API

A collection of useful tools powered by Open API schema

## Installation

Install Python 3.6 (other versions may be compatible)

Clone this repository

```bash
git clone https://github.com/specify/open-api
cd open-api
```

Configure a virtual environment

```bash
python -m venv venv
```

Install the dependencies

```bash
./venv/bin/pip install -r requirements.txt
```

Install this package locally

```bash
pip install -e .
```

## Testing API

### (Optional) Define parameter constrains

If response object depends on the query parameters, you can
test for these relationships by adding your parameter names
and handler function to the `parameter_constraints` dictionary
in `src/validate/parameter_constraints.py`.

Each handler function would receive the following arguments:

* parameter_value (bool): the value of the parameter this handler
  works with
* path (str): name of the current endpoint (useful if the same
  parameter is shared between multiple endpoints)
* response (any): full response object

The handler function should return a boolean value saying validating
whether the response object is as expected

### Run the test

Run the test

```python
import open_api_tools.test.full_test as full_test

def error_callback(*error_message):
    print(error_message)

full_test.test(
    open_api_schema_location='open_api.yaml',
    error_callback=error_callback,
    max_urls_per_endpoint=50,
    failed_request_limit=10,
    parameter_constraints={}
)
```

This script would automatically generate test URLs based on
your API schema.

All requests would be sent to the first server
specified in the `servers` part of the API schema.

### Supplying test values for parameters

By default, the test reads the `examples` object
[in the schema](https://swagger.io/specification/#example-object) to generate
request parameters. If `examples` wasn't provided, it would try to create some
test values based on the parameter type.

If you would like more customization, an optional `parameter_values_generator`
parameter can be provided to the `full_test.test` method.
`parameter_values_generator` must be a function that accept endpoint name as
the first parameter and
[the parameter object](https://swagger.io/specification/#parameter-object)
as the second parameter (it would vary depending on how it is defined
in your schema). In turn, the function must return a list of valid examples.

Example usage:

```python
import open_api_tools.test.full_test as full_test

def error_callback(*error_message):
    print(error_message)

def parameter_values_generator(endpoint_name, parameter):
    return [parameter.name, endpoint_name, *parameter.examples]

full_test.test(
    open_api_schema_location='open_api.yaml',
    error_callback=error_callback,
    max_urls_per_endpoint=50,
    failed_request_limit=10,
    parameter_constraints={}
)
```
