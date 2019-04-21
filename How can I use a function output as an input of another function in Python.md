## How can I use a function output as an input of another function in Python?

You invoke function by referring to its name and providing an argument list, in parentheses, immediately after the (lexical) reference. If you refer to a function without invoking it, then that is a reference which can be treated as a “first class” value (just as you would with any number, string, or any other type of data.

For example:

```
def increment(num):    return num + 1 def square(num):    return num * num nine = square(increment(2))five = increment(square(2)) class NumHooker:    def __init__(self):        self.funclist = list()        self.add(lambda x: x)    def __call__(self, num):        result = num        for each_func in self.funclist:            result = each_func(result)        return result    def add(self, func):        self.funclist.append(func) incsquare = NumHooker()incsquare.add(increment)incsquare.add(square) five = incsquare(2)
```

Through line 5 we're just defining a couple of toy functions. They're deliberately trivial so we can focus on the how we’re using them rather than what they do.

The next couple on lines are simple examples of the usage, nested as a hint towards where we’re going in the next example. The results should be unsurprising.

Next, on line 10, I create a class which allows us to intantiate an object which is callable (instances act like functions), and which can apply a dynamically defined sequence of functions to a single argument.

The *add* method to this class takes a reference to a function as an argument and simply append each of those to a list. The *__call__()* special method simply iterates across the function list applying each function to the previous (intermediary) result.

In the *__init__()* special method, I initialize the *funclist* with a “do nothing” or “identity” lambda. The Python *lambda* statement takes an expression and returns an anonymous function. This particular lambda is just there to ensure that the *funclist* is never empty, it can always be called at least once.

Of course the rest shows an example of using an instance of this class to created a function combining (composing) the two example functions into one callable object, which acts just like a function.

Of course this example is a toy. But imagine a more elaborate class with methods to support a comple syntax tree. One might build a web or graphical user interface or custom parser which generates these objects, each of which can apply arbitrary trees of operations on arbitrary argument lists.

This ability to pass callable objects around as values, to dynamically compose them in various ways, and then call them is used in various functional programming models. Python supports currying, partial function application and other functional semantics (though it is NOT a functional language in the formal sense of that term,



<https://www.quora.com/How-can-I-use-a-function-output-as-an-input-of-another-function-in-Python>