---
title: Storing and executing logic from a database
permalink: / ru / store-and-execute-logic-from-database /
categories: ['Ruby', 'Tutorials', 'Database']
tags: ['ruby', 'database']

---

Solving one of the tasks in my work, I was faced with the problem of storing and executing some logic from the database.
I will give a specific example.
It is necessary to show any text blocks to our users, depending on the properties of the users.
<! - more ->


The task is easy and the solution is to impose these blocks and write code to show these blocks.
And what if we want to get rid of the work of the programmer and let the site administrator add these blocks himself
and specify the conditions of the show just "stumbling" conditions through the form of creating these blocks?

Here the task becomes a little more difficult. Creating blocks and storing them in the database is easy in principle.
It remains the moment of preservation of these conditions in the database, parsing and execution of these conditions.

Since the main language in which I write the code was Ruby, the site administrator could provide a field for
inputting logic in ruby ​​code, but from a security point this is not a good solution.
So you need to write a language or DSL to describe the conditions, but why reinvent the wheel, because most likely libraries have been written for such needs.
And then I began to search for libraries that would fit my needs.

The first library that caught my eye was Dentaku

## Dentaku
[Dentaku] (https://github.com/rubysolo/dentaku) is a logical formula language parser that allows you to substitute your own user variables into the original expression.
The language in which you can write expressions for this analyzer is a kind of SQL.

Let's try this library in action!

Take a specific task:

 - Show a unique offer to every tenth user,
   who made a purchase on January 28 in our store

Suppose that a user sends us such data:

```ruby

user_params_json  = <<-JSON
{ 
  "client_id": 110,
  "last_payment_date": "2018-1-28"
}
JSON
```

Let's write terms using the dentaku library

```ruby
require 'dentaku'
require 'json'

expression = '
  client_id % 10 = 0 AND
  last_payment_date = "2018-1-28"
'

calculator = Dentaku::Calculator.new
calculator.store(JSON.parse(user_params_json))
calculator.evaluate(expression)
# => true
```

Great, the library is working!
Now the administrator of our site can simply create a unique offer, drive conditions for display through the GUI and save it to the database.
And we just need to get from the database a unique offer and compare the conditions of the show with the user parameters.
#### Speed
And what about speed? After all, the conditions are parsed, the AST tree is created, and only then the code is executed, so yes, the Dentaku library works
The fulfillment of this condition showed a speed of applied equal to 3000 operations per second.
This option seemed slow to me, so I went further to look for the appropriate libraries.
## JsonLogic

The next library we’ll look at is [JsonLogic] (http://jsonlogic.com/)
We will use the library implementation written in Ruby - [json_logic] (https://github.com/bhgames/json-logic-ruby)

json_logic allows you to create and store conditions in JSON format, and also allows you to insert custom variables into conditions.

We will solve the same problem

User settings will remain the same:

```ruby
user_params_json  = <<-JSON
{ 
  "client_id": 110,
  "last_payment_date": "2018-1-28"
}
JSON
```
Let's write the terms using the JSON format

`` ruby
require 'json_logic'
require 'json'

rules_json = << - JSON
{"and": [
  {"==": [{"%": [{"var": "client_id"}, 10]}, 0]},
  {"==": [{"var": "last_payment_date"}, "2018-1-28"]}
]}
Json

rules = JSON.parse (rules_json)
data = JSON.parse (user_params_json)
JSONLogic.apply (rules, data)
# => true
```
### Speed
The fulfillment of this condition showed a speed of applied equal to 19,600 operations per second.

I like this speed! I think this library will suit me.

It remains to write the GUI so that the administrator of our site wrote the conditions not in text form, but using a convenient form for entering conditions.
