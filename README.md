# pywit

`pywit` is the Python SDK for [Wit.ai](http://wit.ai).

## Install

Using `pip`:
```bash
pip install wit
```

From source:
```bash
git clone https://github.com/wit-ai/pywit
python setup.py install
```

## Usage

See the `examples` folder for examples.

## API

### Versioning

The default API version is `20160516`.
You can target a specific version by setting the env variable `WIT_API_VERSION`.

### Overview

`pywit` provides a Wit class with the following methods:
* `message` - the Wit [message API](https://wit.ai/docs/http/20160330#get-intent-via-text-link)
* `converse` - the low-level Wit [converse API](https://wit.ai/docs/http/20160330#converse-link)
* `run_actions` - a higher-level method to the Wit converse API
* `interactive` - starts an interactive conversation with your bot

### Wit class

The Wit constructor takes the following parameters:
* `token` - the access token of your Wit instance
* `actions` - the dictionary with your actions

`actions` has action names as keys and action implementations as values.
You need to provide at least an implementation for the special actions `say`, `merge` and `error`.

A minimal `actions` dict looks like this:
```python
def say(session_id, context, msg):
    print(msg)

def merge(session_id, context, entities, msg):
    return context

def error(session_id, context, e):
    print(str(e))

actions = {
    'say': say,
    'merge': merge,
    'error': error,
}
```

A custom action takes the following parameters:
* `session_id` - a unique identifier describing the user session
* `context` - the dictionary representing the session state

Example:
```python
from wit import Wit
client = Wit(token, actions)
```

### Logging

Default logging is to `STDOUT` with `INFO` level.

You can set your logging level as follows:
``` python
from wit import Wit
import logging
client = Wit(token, actions)
client.logger.setLevel(logging.WARNING)
```

You can also specify a custom logger object in the Wit constructor:
``` python
from wit import Wit
client = Wit(token, actions, logger=custom_logger)
```

See the [logging module](https://docs.python.org/2/library/logging.html) and
[logging.config](https://docs.python.org/2/library/logging.config.html#module-logging.config) docs for more information.

### message

The Wit [message API](https://wit.ai/docs/http/20160330#get-intent-via-text-link).

Takes the following parameters:
* `msg` - the text you want Wit.ai to extract the information from
* `verbose` - (optional) if set, calls the API with `verbose=true`

Example:
```python
resp = client.message('what is the weather in London?')
print('Yay, got Wit.ai response: ' + str(resp))
```

### run_actions

A higher-level method to the Wit converse API.

Takes the following parameters:
* `session_id` - a unique identifier describing the user session
* `message` - the text received from the user
* `context` - the dict representing the session state
* `max_steps` - (optional) the maximum number of actions to execute (defaults to 5)
* `verbose` - (optional) if set, calls the API with `verbose=true`

Example:
```python
session_id = 'my-user-session-42'
context0 = {}
context1 = client.run_actions(session_id, 'what is the weather in London?', context0)
print('The session state is now: ' + str(context1))
context2 = client.run_actions(session_id, 'and in Brussels?', context1)
print('The session state is now: ' + str(context2))
```

### converse

The low-level Wit [converse API](https://wit.ai/docs/http/20160330#converse-link).

Takes the following parameters:
* `session_id` - a unique identifier describing the user session
* `message` - the text received from the user
* `context` - the dict representing the session state
* `verbose` - (optional) if set, sets the API parameter `verbose` to `true`

Example:
```python
resp = client.converse('my-user-session-42', 'what is the weather in London?', {})
print('Yay, got Wit.ai response: ' + str(resp))
```

See the [docs](https://wit.ai/docs) for more information.

### interactive

Starts an interactive conversation with your bot.

Example:
```python
client.interactive()
```

See the [docs](https://wit.ai/docs) for more information.
