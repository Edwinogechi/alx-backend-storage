Project - 0x02 Redis Basic
Description
This project focuses on exploring the basics of Redis, an open-source, in-memory data structure store used as a database, cache, and message broker. Redis is known for its speed and versatility, offering various data structures and features that make it suitable for a wide range of use cases, including caching, real-time analytics, messaging, and more.

Features
Introduction to Redis: Get familiar with Redis and its key concepts, such as data types, commands, and persistence options.
Data Structure Manipulation: Learn how to work with Redis data structures like strings, lists, sets, hashes, and sorted sets.
Persistence and Replication: Understand Redis persistence mechanisms and replication for data durability and fault tolerance.
Performance Optimization: Explore techniques for optimizing Redis performance, including pipelining, clustering, and sharding.
Real-world Use Cases: Discover practical applications of Redis in scenarios such as caching, session management, job queues, and real-time analytics.
Installation
To start working with Redis, follow these steps:

Download and install Redis from the official website.
Install the Redis client for your programming language of choice (e.g., redis-py for Python, hiredis for C).
Start the Redis server by running redis-server in your terminal.
Connect to the Redis server from your application using the appropriate client library.
Usage
Once Redis is installed and running, you can start using it in your applications:

Basic Operations: Perform basic operations like storing and retrieving data using Redis commands.
Data Structure Manipulation: Use Redis commands to manipulate various data structures such as strings, lists, sets, hashes, and sorted sets.
Persistence and Replication: Configure Redis persistence options and replication for data durability and fault tolerance.
Performance Optimization: Implement performance optimization techniques like pipelining, clustering, and sharding based on your application requirements.
Examples
Here are some examples of using Redis in different scenarios:

Caching: Store frequently accessed data in Redis to reduce database load and improve application performance.
Session Management: Use Redis to manage session data for web applications, ensuring scalability and high availability.
Job Queues: Implement a job queue using Redis to handle background tasks and asynchronous processing.
Real-time Analytics: Store and analyze real-time data streams with Redis data structures like sorted sets and hyperloglogs.
Contributing
Contributions to this project are welcome! If you have ideas for improvements, bug fixes, or new features related to Redis basics, feel free to submit a pull request.

Please ensure that your code adheres to the project's coding conventions and includes appropriate documentation and tests.

License
This project is licensed under the MIT License - see the LICENSE file for details.

Acknowledgements
Special thanks to the Redis community for developing and maintaining such a powerful and versatile tool.

Contact
For questions or suggestions regarding this project, feel free to contact the project maintainer: Edwin Ogechi

Tasks
0. Writing strings to Redis
mandatory
Create a Cache class. In the __init__ method, store an instance of the Redis client as a private variable named _redis (using redis.Redis()) and flush the instance using flushdb.

Create a store method that takes a data argument and returns a string. The method should generate a random key (e.g. using uuid), store the input data in Redis using the random key and return the key.

Type-annotate store correctly. Remember that data can be a str, bytes, int or float.

1. Reading from Redis and recovering original type
mandatory
Redis only allows to store string, bytes and numbers (and lists thereof). Whatever you store as single elements, it will be returned as a byte string. Hence if you store "a" as a UTF-8 string, it will be returned as b"a" when retrieved from the server.

In this exercise we will create a get method that take a key string argument and an optional Callable argument named fn. This callable will be used to convert the data back to the desired format.

Remember to conserve the original Redis.get behavior if the key does not exist.

Also, implement 2 new methods: get_str and get_int that will automatically parametrize Cache.get with the correct conversion function.

The following code should not raise:

cache = Cache()

TEST_CASES = {
    b"foo": None,
    123: int,
    "bar": lambda d: d.decode("utf-8")
}

for value, fn in TEST_CASES.items():
    key = cache.store(value)
    assert cache.get(key, fn=fn) == value

2. Incrementing values
mandatory
Familiarize yourself with the INCR command and its python equivalent.

In this task, we will implement a system to count how many times methods of the Cache class are called.

Above Cache define a count_calls decorator that takes a single method Callable argument and returns a Callable.

As a key, use the qualified name of method using the __qualname__ dunder method.

Create and return function that increments the count for that key every time the method is called and returns the value returned by the original method.

Remember that the first argument of the wrapped function will be self which is the instance itself, which lets you access the Redis instance.

Protip: when defining a decorator it is useful to use functool.wraps to conserve the original function’s name, docstring, etc. Make sure you use it as described here.

Decorate Cache.store with count_calls.

3. Storing lists
mandatory
Familiarize yourself with redis commands RPUSH, LPUSH, LRANGE, etc.

In this task, we will define a call_history decorator to store the history of inputs and outputs for a particular function.

Everytime the original function will be called, we will add its input parameters to one list in redis, and store its output into another list.

In call_history, use the decorated function’s qualified name and append ":inputs" and ":outputs" to create input and output list keys, respectively.

call_history has a single parameter named method that is a Callable and returns a Callable.

In the new function that the decorator will return, use rpush to append the input arguments. Remember that Redis can only store strings, bytes and numbers. Therefore, we can simply use str(args) to normalize. We can ignore potential kwargs for now.

Execute the wrapped function to retrieve the output. Store the output using rpush in the "...:outputs" list, then return the output.

Decorate Cache.store with call_history.

4. Retrieving lists
mandatory
In this tasks, we will implement a replay function to display the history of calls of a particular function.

Use keys generated in previous tasks to generate the following output:

>>> cache = Cache()
>>> cache.store("foo")
>>> cache.store("bar")
>>> cache.store(42)
>>> replay(cache.store)
Cache.store was called 3 times:
Cache.store(*('foo',)) -> 13bf32a9-a249-4664-95fc-b1062db2038f
Cache.store(*('bar',)) -> dcddd00c-4219-4dd7-8877-66afbe8e7df8
Cache.store(*(42,)) -> 5e752f2b-ecd8-4925-a3ce-e2efdee08d20
Tip: use lrange and zip to loop over inputs and outputs.

5. Implementing an expiring web cache and tracker
#advanced
In this tasks, we will implement a get_page function (prototype: def get_page(url: str) -> str:). The core of the function is very simple. It uses the requests module to obtain the HTML content of a particular URL and returns it.

Start in a new file named web.py and do not reuse the code written in exercise.py.

Inside get_page track how many times a particular URL was accessed in the key "count:{url}" and cache the result with an expiration time of 10 seconds.

Tip: Use http://slowwly.robertomurray.co.uk to simulate a slow response and test your caching.

Bonus: implement this use case with decorators.
