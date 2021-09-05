---
title: "Storing Nested Objects in Redis"
date: "2018-03-24"
template: "post"
draft: false
slug: "storing-nested-objects-in-redis"
category: "Software Engineering"
tags:
  - "redis"
  - "cache"
description: "One way to optimize your applications is through caching. It helps to minimize database transactions by storing frequently accessed data to memory, which results to fast retrieval of data. I use Redis for caching data, but at the time of this writing Redis does not yet support saving of nested objects. This article will walk you through to a simple solution to save nested objects on Redis efficiently."
---

One way to optimize your applications is through caching. It helps to minimize database transactions by storing frequently accessed data to memory, which results to fast retrieval of data. I use Redis for caching data, among other things, because it is flexible enough and easy to use. Redis is a key-value store. Meaning you have this unique key to access the stored value. These value can be a string, list, sets, hashes, sorted sets, and bitmaps and hyperloglogs.

As an API developer, I work with objects most of the time and it includes caching of these objects. It says in the Redis Data-type documentation that hashes data-type is the perfect fit for caching object-like data.

Here’s an example how to cache/retrieve object-like data in Redis using hash data-type.

```
dict_object = {"firstname": "John", "lastname": "Doe"}
type(dict_object)  # dict

# cache dict_object as redis hash
app.redis_connection.hmset("your_unique_key", dict_object)

# fetch cached data (hash) from redis 
cached_data = app.redis_connection.hgetall("your_unique_key")
type(cached_data)  # dict
```

But this is fine when you only cache a flat objects or one dimensional objects. Currently Redis hashes doesn’t support nested objects. So what would happen if you try to store nested object as hash in Redis? Lets try:

```
dict_object = {
	"name": "John Doe",
	"social_media_accounts": ["facebook", "twitter", "Instagram"],
	"language_proficiency": {
		"Python": "9/10", 
		"Javascript": "7/10",
		"Ruby": "8/10"
	}
}

type(dict_object)  # dict
type(dict_object["social_media_accounts"])  # dict
type(dict_object["language_proficiency"])  # list

# cache dict_object as redis hash
app.redis_connection.hmset("your_unique_key", dict_object)

# fetch cached data (hash) from redis
cached_data = app.redis_connection.hgetall("your_unique_key") 
type(cached_data)  # dict
type(cached_data["social_media_accounts"])  # string
type(cached_data["language_proficiency"])  # string
```

As you can see, we didn’t get the expected data type of our nested objects . Parsing these nested objects manually would be painful. The better approach here is to cache the object as string. In this way, when we retrieve our data from cache it would be easier to parse it as an object (JSON or dictionary) and we’ll get the expected data-type of each inner objects.

Here’s an example how to cache/retrieve object-like data in Redis using string data-type.

```
dict_object = {
	"name": "John Doe",
	"social_media_accounts": ["facebook", "twitter", "Instagram"],
	"language_proficiency": {
		"Python": "9/10", 
		"Javascript": "7/10",
		"Ruby": "8/10"
	}
}

# convert your dict object to string
stringified_dict_obj = json.dumps(dict_object)
type(stringified_dict_obj)  # str

# cache stringified dict_object
app.redis_connection.set("your_unique_key", stringified_dict_obj)

# fetch cached data (string) from redis
cached_data = app.redis_connection.get("your_unique_key") 
type(cached_data)  # str

# convert your string cached data to object(dict/JSON)
cached_data_as_dict = json.loads(cached_data)  
type(cached_data_as_dict)  # dict
type(cached_data_as_dict["social_media_accounts"])  # list
type(cached_data_as_dict["language_proficiency"])  # dict
```

There are things you need to consider by using this solution for caching nested objects:

- Storing and retrieving nested object as Hash or String in Redis doesn’t have that much difference in processing time. The big difference here will be at the time you retrieved the data and convert it to object. If you store nested object-like data as Hash you would manually convert each inner object into there respective data-type, while object-like data stored as string is much simpler which is to convert the whole string as object.
- By storing object-like data as hash, you can get the value of a specific field/key without retrieving the whole object, on the other hand, you can’t do this on the string data-type. You’ll going to retrieve the whole cached data before you get the value that you want.

You can checkout this [gist](https://gist.github.com/ryanermita/6371da82e1e2fd49806148cd3e54b979) to play around with redis caching in python. Also I love to know if there are things that I missed regarding this topic, lets talk in the comment section. Happy Coding!

### Resources:

- [Redis data-types](https://redis.io/topics/data-types)
-[ Useful Caching Technologies PHP Application](http://www.techthings.org/useful-caching-technologies-php-application)
- [Storing Relational Data in Redis](https://alexandergugel.svbtle.com/storing-relational-data-in-redis)