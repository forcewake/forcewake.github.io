---
published: false
---

A short update about several interesting issues that i faced in today

{% include _toc.html %}

## How-to use Protobuf
You should install (protobuf-net)[https://www.nuget.org/packages/protobuf-net] from nuget and then:

{% highlight csharp %}
// Explicit proto contract
[ProtoContract]
public class MappedEntity
{
    [ProtoMember(1)]
    public int Id { get; set; }

    [ProtoMember(2)]
    public string Name { get; set; }
}

var entity = new MappedEntity { Id = 1, Name = "123" };

// Serialize/deserialize using protobuf-net
byte[] serialized;
using (var ms = new MemoryStream())
{
    Serializer.Serialize(ms, entity);
    serialized = ms.ToArray();
}

MappedEntity deserialized;
using (var ms = new MemoryStream(serialized))
{
    deserialized = Serializer.Deserialize<MappedEntity>(ms);
}
{% endhighlight %}

More information about usage, issues and problems in Protobuf you can get from (this article [ru])[http://devoyster.blogspot.com.by/2011/12/protobuf-net.html]

## Replacing switch statements with Object

Let’s look at a usual switch statement:
{% highlight javascript %}
var type = 'coke';
var drink;
switch(type) {
case 'coke':
  drink = 'Coke';
  break;
case 'pepsi':
  drink = 'Pepsi';
  break;
default:
  drink = 'Unknown drink!';
}
console.log(drink); // 'Coke'
{% endhighlight %}

### Object Literal lookups

All know roblems with switch, so let’s refactor and setup a simple Object literal that returns a String value only:
{% highlight javascript %}
function getDrink (type) {
  var drinks = {
    'coke': 'Coke',
    'pepsi': 'Pepsi',
    'lemonade': 'Lemonade',
    'default': 'Default item'
  };
  return 'The drink I chose was ' + (drinks[type] || drinks['default']);
}

var drink = getDrink('coke');
// The drink I chose was Coke
console.log(drink);
{% endhighlight %}

We’ve saved a few lines of code from the switch, and to me the data is a lot cleaner in presentation. We can even simplify it further, without a default case:

{% highlight javascript %}
function getDrink (type) {
  return 'The drink I chose was ' + {
    'coke': 'Coke',
    'pepsi': 'Pepsi',
    'lemonade': 'Lemonade'
  }[type];
}
{% endhighlight %}

We might, however, need more complex code than a String, which could hang inside a function. For sake of brevity and easy to understand examples, I’ll just return the above strings from the newly created function:

{% highlight javascript %}
function getDrink (type) {
  var drinks = {
    'coke': function () {
      return 'Coke';
    },
    'pepsi': function () {
      return 'Pepsi';
    },
    'lemonade': function () {
      return 'Lemonade';
    },
    'default': function () {
      return 'Default item';
    }
  };
  return (drinks[type] || drinks['default'])();
}
{% endhighlight %}

This wraps the two Object lookups inside parenthesis `( )`, treating them as an expression. The result of the expression is then invoked. If `drinks[type]` isn’t found in the lookup, it’ll default to `drinks['default']`, simple!

We don’t have to always return inside the function either, we can change references to any variable then return it:

{% highlight javascript %}
function getDrink (type) {
  var drink;
  var drinks = {
    'coke': function () {
      drink = 'Coke';
    },
    'pepsi': function () {
      drink = 'Pepsi';
    },
    'lemonade': function () {
      drink = 'Lemonade';
    },
    'default': function () {
      drink = 'Default item';
    }
  };
    
  // invoke it
  (drinks[type] || drinks['default'])();
    
  // return a String with chosen drink
  return 'The drink I chose was ' + drink;
}

var drink = getDrink('coke');
// The drink I chose was Coke
console.log(drink);
{% endhighlight %}

### Object Literal “fall through”

With switch cases, we can let them fall through (which means more than one case can apply to a specific piece of code):

{% highlight javascript %}
var type = 'coke';
var snack;
switch(type) {
case 'coke':
case 'pepsi':
  snack = 'Drink';
  break;
case 'cookies':
case 'crisps':
  snack = 'Food';
  break;
default:
  drink = 'Unknown type!';
}
console.log(snack); // 'Drink'
{% endhighlight %}

We let coke and pepsi “fall through” by not adding a break statement. Doing this for Object Literals is simple and more declarative - as well as being less prone to error. Our code suddenly becomes much more structured, readable and reusable:

{% highlight javascript %}
function getSnack (type) {
  var snack;
  function isDrink () {
    return snack = 'Drink';
  }
  function isFood () {
    return snack = 'Food';
  }
  var snacks = {
    'coke': isDrink,
    'pepsi': isDrink,
    'cookies': isFood,
    'crisps': isFood,
  };
  return snacks[type]();
}

var snack = getSnack('coke');
console.log(snack); // 'Drink'
{% endhighlight %}

### Summing up

Object literals are a more natural control of flow in JavaScript, switch is a bit old and clunky and prone to difficult debugging errors. Object’s are more extensible, maintainable, and we can test them a lot better. 