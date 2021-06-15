## Making Control Flow Easy to Read

If code had no conditionals, loops, or any other control flow statements, it would be very easy to read. These jumps and branches are the hard stuff, where code can get confusing quickly. This chapter is about making the control flow in your code easy to read.

> Make all your conditionals, loops, and other changes to control flow as “natural” as possible—written in a way that doesn’t make the reader stop and reread your code.

### The Order of Arguments in Conditionals

Which of these two pieces of code is more readable:

```python
if length >= 10:
	pass

# or

if 10 <= length:
	pass
```

To most programmers, the first is much more readable. But what about the next two:

```python
while (bytes_received < bytes_expected):
	pass

# or

while (bytes_expected > bytes_received):
	pass
```

Again, the first version is more readable. But why? What’s the general rule? How do you decide whether it’s better to write `a < b or b > a`?

| Left-hand side      | Right-hand side |
| ----------- | ----------- |
| The expression “being interrogated,” whose value is more in flux.      | The expression being compared against, whose value is more constant.       |

### The Order of if/else Blocks

When writing an if/else statement, you usually have the freedom to swap the order of the blocks. For instance, you can either write it like:

```cpp
if (a == b) {
    // Case One ...
} else {
    // Case Two ...
}

// OR

if (a != b) {
    // Case Two ...
} else {
    // Case One ...
}
```

You may not have given much thought about this before, but in some cases there are good reasons to prefer one order over the other:

- Prefer dealing with the *positive* case first instead of the negative—e.g., `if (debug)` instead of `if (!debug)`.
- Prefer dealing with the *simpler* case first to get it out of the way. This approach might also allow both the `if` and the `else` to be visible on the screen at the same time, which is nice.
- Prefer dealing with the more *interesting* or conspicuous case first.

For example, suppose you have a web server that’s building a `response` based on whether the URL contains the query parameter `expand_all`:

```cpp
if (!url.HasQueryParameter("expand_all")) {
    response.Render(items);
    ...
} else {
    for (int i = 0; i < items.size(); i++) {
        items[i].Expand();
    }
    ...
}
```

When the reader glances at the first line, her brain immediately thinks about the `expand_all` case. It’s like when someone says, “Don’t think of a pink elephant.” You can’t help but think about it—the “don’t” is drowned out by the more unusual “pink elephant.”

Here, `expand_all` is our pink elephant. Because it’s the more interesting case (and it’s the positive case, too), let’s deal with it first:

```cpp
if (url.HasQueryParameter("expand_all")) {
    for (int i = 0; i < items.size(); i++) {
        items[i].Expand();
    }
    ...
} else {
    response.Render(items);
    ...
}
```

On the other hand, here’s a situation where the negative case *is* the simpler and more interesting/dangerous one, so we deal with it first:

```c
if not file:
    # Log the error ...
else:
    # ...
```

To summarize, our advice is simply to pay attention to these factors and watch out for cases where your if/else is in an awkward order.

### The ?: Conditional Expression (a.k.a. “Ternary Operator”)

Its effect on readability is controversial. Proponents think it’s a nice way to write something in one line that would otherwise require multiple lines. Opponents argue that it can be confusing to read and difficult to step through in a debugger

Here’s a case where the ternary operator is readable and compact:

```c
time_str += (hour >= 12) ? "pm" : "am";

// Avoiding the ternary operator, you might write:

if (hour >= 12) {
    time_str += "pm";
} else {
    time_str += "am";
}

// which is a bit drawn out and redundant. In this case, a conditional expression seems reasonable.
```

> Instead of minimizing the number of lines, a better metric is to minimize the time needed for someone to understand it.

Advice →

> By default, use an if/else. The ternary ?: should be used only for the simplest cases.

### Returning Early from a Function

Some coders believe that functions should never have multiple return statements. This is nonsense. Returning early from a function is perfectly fine—and often desirable. For example:

```java
public boolean Contains(String str, String substr) {
    if (str == null || substr == null) return false;
    if (substr.equals("")) return true;

    ...
}
```

### Minimize Nesting

Deeply nested code is hard to understand. Each level of nesting pushes an extra condition onto the reader’s “mental stack.” When the reader sees a closing brace (`}`) it can be hard to “pop” the stack and remember what condition is underneath.

Here is a relatively simple example of this—see if you notice yourself looking back up to double-check which block conditions you’re in:

```c
if (user_result == SUCCESS) {
    if (permission_result != SUCCESS) {
        reply.WriteErrors("error reading permissions");
        reply.Done();
        return;
    }
    reply.WriteErrors("");
} else {
    reply.WriteErrors(user_result);

}
reply.Done();
```

When you see that first closing brace, you have to think to yourself, *Oh,* `permission_result != SUCCESS` *has just ended, so now* `permission_result == SUCCESS`*, and this is still inside the block where* `user_result == SUCCESS`*.*

Overall, you have to keep the values of `user_result` and `permission_result` in your head at all times. And as each `if { }` block closes, you have to toggle the corresponding value in your mind.

This particular code is even worse because it keeps alternating between the `SUCCESS` and non-`SUCCESS` situations.

> Look at your code from a fresh perspective when you’re making changes. Step back and look at it as a whole.

### Removing Nesting by Returning Early

Okay, so let’s improve the code. Nesting like this can be removed by handling the “failure cases” as soon as possible and returning early from the function:

```c
if (user_result != SUCCESS) {
    reply.WriteErrors(user_result);
    reply.Done();
    return;
}

if (permission_result != SUCCESS) {
    reply.WriteErrors(permission_result);
    reply.Done();
    return;
}

reply.WriteErrors("");
reply.Done();
```

This code only has one level of nesting, instead of two. But more importantly, the reader never has to “pop” anything from his mental stack—every if block ends in a return.

### Removing Nesting Inside Loops

The technique of returning early isn’t always applicable. For example, here’s a case of code nested in a loop:

```c
for (int i = 0; i < results.size(); i++) {
    if (results[i] != NULL) {
        non_null_count++;

        if (results[i]->name != "") {
            cout << "Considering candidate..." << endl;

            ...
        }
    }
}
```

Inside a loop, the analogous technique to returning early is to `continue`:

```cpp
for (int i = 0; i < results.size(); i++) {
    if (results[i] == NULL) continue;
    non_null_count++;

    if (results[i]->name == "") continue;
    cout << "Considering candidate..." << endl;

    ...
}
```

### **Summary**

There are a number of things you can do to make your code’s control flow easier to read.

When writing a comparison (`while (bytes_expected > bytes_received)`), it’s better to put the changing value on the left and the more stable value on the right (`while (bytes_received < bytes_expected)`).

You can also reorder the blocks of an `if/else` statement. Generally, try to handle the positive/easier/interesting case first. Sometimes these criteria conflict, but when they don’t, it’s a good rule of thumb to follow.

Certain programming constructs, like the ternary operator (`: ?`), the `do/while` loop, and `goto` often result in unreadable code. It’s usually best not to use them, as clearer alternatives almost always exist.

Nested code blocks require more concentration to follow along. Each new nesting requires more context to be “pushed onto the stack” of the reader. Instead, opt for more “linear” code to avoid deep nesting.

Returning early can remove nesting and clean up code in general. “Guard statements” (handling simple cases at the top of the function) are especially useful.
gfh