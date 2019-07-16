[Python converting string to tuple without splitting characters](https://stackoverflow.com/questions/16449184/python-converting-string-to-tuple-without-splitting-characters)

[Using python's eval() vs. ast.literal_eval()?](https://stackoverflow.com/questions/15197673/using-pythons-eval-vs-ast-literal-eval)



`datamap = eval(raw_input('Provide some data here: '))` means that you actually evaluate the code *before* you deem it to be unsafe or not. It evaluates the code as soon as the function is called. See also [the dangers of `eval`](http://nedbatchelder.com/blog/201206/eval_really_is_dangerous.html).

`ast.literal_eval` raises an exception if the input isn't a valid Python datatype, so the code won't be executed if it's not.

Use `ast.literal_eval` whenever you need `eval`. You shouldn't usually evaluate literal Python statements.



`ast.literal_eval()` only considers a small subset of Python's syntax to be valid:

```
The string or node provided may only consist of the following Python literal structures: strings, numbers, tuples, lists, dicts, booleans, and None.
```

Passing `__import__('os').system('rm -rf /a-path-you-really-care-about')` into `ast.literal_eval()` will raise an error, but `eval()` will happily wipe your drive.

Since it looks like you're only letting the user input a plain dictionary, use `ast.literal_eval()`. It safely does what you want and nothing more.