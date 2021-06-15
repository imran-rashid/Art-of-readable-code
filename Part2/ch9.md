## Variables and Readability

### Eliminating Variables

In this section, we’re interested in eliminating variables that don’t improve readability. When a variable like this is removed, the new code is more concise and just as easy to understand.

Useless Temporary Variables →

```python
import datetime
now = datetime.datetime.now()
root_message.last_view_time =now
```

Is `now` a variable worth keeping? No, and here are the reasons:

- It isn’t breaking down a complex expression.
- It doesn’t add clarification—the expression `datetime.datetime.now()` is clear enough.
- It’s used only once, so it doesn’t compress any redundant code.

Without `now`, the code is just as easy to understand:

```python
root_message.last_view_time = datetime.datetime.now()
```

Eliminating Intermediate Results →

Here’s an example of a JavaScript function that removes a value from an array:

```jsx
var remove_one = function (array, value_to_remove) {
    var index_to_remove = null;

    for (var i = 0; i < array.length; i += 1) {
        if (array[i] === value_to_remove) {
            index_to_remove = i;
            break;
        }
    }

    if (index_to_remove !== null) {
        array.splice(index_to_remove, 1);
    }
}
```

The variable `index_to_remove` is just used to hold an *intermediate result*. Variables like this can sometimes be eliminated by handling the result as soon as you get it:

```jsx
var remove_one = function (array, value_to_remove) {
    for (var i = 0; i < array.length; i += 1) {
        if (array[i] === value_to_remove) {
            array.splice(i, 1);
            return;
        }
    }
};
```

By allowing the code to return early, we got rid of `index_to_remove` altogether and simplified the code quite a bit.

In general, it’s a good strategy to **complete the task as quickly as possible**.

### Eliminating Control Flow Variables

Sometimes you’ll see this pattern of code in loops:

```java
boolean done = false;

while (/* condition */ && !done) {
    ...

    if (...) {
        done = true;
        continue;
    }
}
```

The variable `done` might even be set to `true` in multiple places throughout the loop.

Variables like `done` are what we call “control flow variables.” Their sole purpose is to steer the program’s execution—they don’t contain any real program data. In our experience, control flow variables can often be eliminated by making better use of structured programming:

```cpp
while (/* condition */) {
    ...
    if (...) {
        break;
    }
}
```

### Shrink the Scope of Your Variables

We’ve all heard the advice to “avoid global variables.” This is good advice, because it’s hard to keep track of where and how all those global variables are being used.

In fact, it’s a good idea to “shrink the scope” of all your variables, not just the global ones.

> Make your variable visible by as few lines of code as possible.

Many programming languages offer multiple scope/access levels, including module, class, function, and block scope. Using more restricted access is generally better because it means the variable can be “seen” by fewer lines of code.

Why do this? Because it effectively reduces the number of variables the reader has to think about at the same time. If you were to shrink the scope of all your variables by a factor of two, then on average there would be half as many variables in scope at any one time.

For example, suppose you have a very large class, with a member variable that’s used by only two methods, in the following way

```cpp
#include <string>

class LargeClass {
    string str_;

    void Method1() {
        str_ = ...;
        Method2();
    }

    void Method2() {
        // Usesstr_
    }

    // Lots of other methods that don't usestr_ ...
};
```

For this case, it may make sense to “demote” `str_` to be a local variable:

```cpp
class LargeClass {
    void Method1() {
        string str = ...;
        Method2 (str);
    }

    void Method2 (string str) {
        // Usesstr
    }

    // Now other methods can't seestr.
};
```

if Statement Scope in C++ →

Suppose you have the following C++ code:

```cpp
PaymentInfo* info = database.ReadPaymentInfo();

if (info) {
    cout << "User paid: " <<info->amount() << endl;
}

// Many more lines of code below ...
```

The variable `info` will remain in scope for the rest of the function, so the person reading this code might keep `info` in mind, wondering if/how it will be used again.

But in this case, `info` is only used inside the `if` statement. In C++, we can actually define `info` in the conditional expression:

```cpp
if (PaymentInfo* info = database.ReadPaymentInfo()) {
    cout << "User paid: " << info->amount() << endl;
}
```

Now the reader can easily forget about `info` after it goes out of scope.

Creating “Private” Variables in JavaScript →

Suppose you have a persistent variable that’s used by only one function:

```jsx
submitted = false;  // Note: global variable

var submit_form = function (form_name) {
    if (submitted) {
        return;  // don't double-submit the form
    }
    ...
    submitted = true;
};
```

Global variables like `submitted` can cause the person reading this code a lot of angst. It seems like `submit_form()` is the only function that uses `submitted`, but you can’t know for sure. In fact, another JavaScript file might be using a global variable named `submitted` too, for a different purpose!

You can prevent this issue by wrapping `submitted` inside a *closure*:

```jsx
var submit_form = (function () {
    varsubmitted = false;  // Note: can only be accessed by the function below

    return function (form_name) {
        if (submitted) {
            return;  // don't double-submit the form
        }
        ...
	submitted = true;
    };
}());
```

Note the parentheses on the last line—the anonymous outer function is immediately executed, returning the inner function.

No Nested Scope in Python and JavaScript →

Languages like C++ and Java have block scope, where variables defined inside an if, for, try, or similar structure are confined to the nested scope of that block:

```cpp
if (...) {
	int x = 1;
}
x++;  // Compile-error! 'x' is undefined.
```

But in Python and JavaScript, variables defined in a block “spill out” to the whole function. For example, notice the use of `example_value` in this perfectly valid Python code:

```python
# No use of example_value up to this point.
if request:
    for value in request.values:
        if value > 0:
            example_value = value
            break

for logger in debug.loggers:
    logger.log("Example:",example_value)
```

The previous example is also buggy: if `example_value` is not set in the first part of the code, the second part will raise an exception: `"NameError: ‘example_value’ is not defined"`. We can fix this, and make the code more readable, by defining `example_value` at the “closest common ancestor” (in terms of nesting) to where it’s used:

```python
example_value = None

if request:
    for value in request.values:
        if value > 0:
            example_value = value
            break

if example_value:
    for logger in debug.loggers:
        logger.log("Example:",example_value)
```

However, this is a case where `example_value` can be eliminated altogether. `example_value` is just holding an intermediate result

Here’s what the new code looks like:

```python
def LogExample(value):
    for logger in debug.loggers:
        logger.log("Example:", value)

if request:
    for value in request.values:
        if value > 0:
            LogExample(value)  # deal with 'value' immediately
            break
```

### Prefer Write-Once Variables

So far in this chapter, we’ve discussed how it’s harder to understand programs with lots of variables “in play.” Well, it’s even harder to think about variables that are constantly changing. Keeping track of their values adds an extra degree of difficulty.

To combat this problem, we have a suggestion that may sound a little strange: **prefer write-once variables**.

Variables that are a “permanent fixture” are easier to think about. Certainly, constants like:

```cpp
static const int NUM_THREADS = 10;
```

don’t require much thought on the reader’s part. And for the same reason, use of `const` in C++ (and `final` in Java) is highly encouraged.

But even if you can’t make your variable write-once, it still helps if the variable changes in fewer places.

> The more places a variable is manipulated, the harder it is to reason about its current value.

Example →

Suppose you have a web page with a number of input text fields, arranged like this:

```html
<input type="text"id="input1" value="Dustin">
<input type="text"id="input2" value="Trevor">
<input type="text"id="input3" value="">
<input type="text"id="input4" value="Melissa">
...
```

Your job is to write a function named `setFirstEmptyInput()` that takes a string and puts it in the first empty `<input>` on the page (in the example shown, `"input3"`). The function should return the DOM element that was updated (or `null` if there were no empty inputs left). Here is some code to do this that *doesn’t* apply the principles in this chapter:

```jsx
var setFirstEmptyInput = function (new_value) {
    var found = false;
    var i = 1;
    var elem = document.getElementById('input' + i);

    while (elem !== null) {
        if (elem.value === '') {
            found = true;
            break;
        }

        i++;
        elem = document.getElementById('input' + i);
    }

    if (found) elem.value = new_value;
    return elem;
};
```

Intermediate variables like `found` can often be eliminated by returning early. Here’s that improvement:

```jsx
var setFirstEmptyInput = function (new_value) {
    var i = 1;
    var elem = document.getElementById('input' + i);

    while (elem !== null) {
        if (elem.value === '') {
            elem.value = new_value;
            return elem;
        }

        i++;
        elem = document.getElementById('input' + i);
    }
    return null;
};
```

Next, take a look at `elem`. It’s used multiple times throughout the code in a very “loopy” way where it’s hard to keep track of its value. The code makes it seem as if `elem` is the value we’re iterating through, when really we’re just incrementing through `i`. So let’s restructure the `while` loop into a `for` loop over `i`:

```jsx
var setFirstEmptyInput = function (new_value) {
    for (var i = 1; true; i++) {
        var elem = document.getElementById('input' + i);
        if (elem === null)
            return null;  // Search Failed. No empty input found.

        if (elem.value === '') {
            elem.value = new_value;
            return elem;
        }
    }
};
```

### **Summary**

This chapter is about how the variables in a program can quickly accumulate and become too much to keep track of. You can make your code easier to read by having fewer variables and making them as “lightweight” as possible. Specifically:

- **Eliminate variables** that just get in the way. In particular, we showed a few examples of how to eliminate “intermediate result” variables by handling the result immediately.
- **Reduce the scope of each variable** to be as small as possible. Move each variable to a place where the fewest lines of code can see it. Out of sight is out of mind.
- **Prefer write-once variables.** Variables that are set only once (or `const`, `final`, or otherwise immutable) make code easier to understand.
