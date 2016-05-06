# Varsity

Varsity helps you define your application's settings, read them from environment
variables, parse them into native Python types, and validate them.

## Load a simple string

Assume the FOO environment variable as been set to '3'.  After setting up the
loader and calling its .load() method, we get back a dictionary with a key for
each setting we've added to the loader.

    >>> from varsity import Loader
    >>> l = Loader()
    >>> l.add('FOO')
    >>> settings = l.load()
    >>> settings.FOO
    '3'

If you want to use a different name for the setting within Python, you can
provide your own.

    >>> l.add('FOO', 'foo')
    >>> settings = l.load()
    >>> settings.foo
    '3'

The object returned from .load() also provides dictionary-style access to
settings.

    >>> settings['foo']
    '3'

You can provide a 'typ' callable that will be used to convert the environment
variable string into the type of your choice.

    >>> l.add('FOO', 'foo', typ=int)
    >>> settings = l.load()
    >>> settings.foo
    3

You can provide defaults that will be returned if the environment variable is
not present.

    >>> l.add('SOME_UNSET_VAR', default=0)
    >>> settings = l.load()
    >>> settings.SOME_UNSET_VAR
    0

If you don't provide a default, and the environment variable is not set,
ValueError will be raised.

You can provide your own callable as the 'typ' argument.  Assume the 'TODAY'
environment variable is set to '2016-05-05'.

    >>> from varsity import Loader
    >>> from iso8601 import parse_date
    >>> l = Loader()
    >>> l.add('TODAY', 'today', typ=lambda x: parse_date(x).date())
    >>> settings = l.load()
    >>> settings.today
    datetime.date(2016, 5, 5)

If you don't provide a 'typ', but you do provide a default, then the environment
variable will be cast to the same type as the default.  (Here we get back an
int 3 instead of the string '3', because the default is an int.)

    >>> l.add('FOO', 'foo', default=0)
    >>> settings = l.load()
    >>> settings.foo
    3

If you access a setting with the attribute-style syntax, then nested
dictionaries can also be accessed with that syntax.  In this example, the FOO
environment variable is set to '{"bar": {"baz": 1.23}}'

    >>> l.add('FOO', 'foo', typ=json.loads)
    >>> settings = l.load()
    >>> settings.foo.bar.baz
    1.23

