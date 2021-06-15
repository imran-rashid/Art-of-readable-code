## Breaking Down Giant Expressions

The giant squid is an amazing and intelligent animal, but its near-perfect body design has one fatal flaw: it has a donut-shaped brain that wraps around its esophagus. So if it swallows too much food at once, it gets brain damage.

What does this have to do with code? Well, code that comes in “chunks” that are too big can have the same kind of effect. Recent research suggests that most of us can only think about three or four “things” at a time. Simply put, the larger an expression of code is, the harder it will be to understand.

> Break down your giant expressions into more digestible pieces.

### Explaining Variables

The simplest way to break down an expression is to introduce an extra variable that captures a smaller subexpression. This extra variable is sometimes called an “explaining variable” because it helps explain what the subexpression means.

```python
if line.split(':')[0].strip() == "root":
	pass
```

Here is the same code, now with an *explaining variable*:

```python
user_name = line.split(':')[0].strip()
if user_name == 'root':
	pass
```

### Summary Variables

Even if an expression doesn’t need explaining (because you can figure out what it means), it can still be useful to capture that expression in a new variable. We call this a *summary variable* if its purpose is simply to replace a larger chunk of code with a smaller name that can be managed and thought about more easily.

```python
if request.user.id == document.owner_id:
	# user can edit

if request.user.id != document.owner_id:
	# user can't edit
```

The main concept in this code is, “Does the user own the document?” That concept can be stated more clearly by adding a summary variable:

```python
user_owns_document = request.user.id == document.owner_id

if user_owns_document:
	# user can edit

if not user_owns_document:
	# user can't edit
```

### Using De Morgan’s Laws

They are two ways to rewrite a Boolean expression into an equivalent one:

```
1)   not (a or b or c)    ⇔   (not a) and (not b) and (not c)
2)   not (a and b and c)  ⇔    (not a) or (not b) or (not c)
```

You can sometimes use these laws to make a Boolean expression more readable

```python
# if (!(file_exists && !is_protected)) Error("Sorry, could not read file.");

if not(file_exists and not(is_protected)):
	AccessError('Sorry, could not read file.')
```

It can be written:

```python
# if (!file_exists || is_protected) Error("Sorry, could not read file.");

if (not file_exists or is_protected):
	AccessError('Sorry, could not read file.')
```

### Abusing Short-Circuit Logic

For example, the statement `if (a or b)` won’t evaluate b if a is true. This behavior is very handy but can sometimes be abused to accomplish complex logic.

```java
assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```

In English, what this code is saying is, “Get the bucket for this key. If the bucket is not `null`, then make sure it isn’t occupied.”

Even though it’s only one line of code, it really makes most programmers stop and think. Now compare it to this code:

```java
bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket->IsOccupied());
```

It does exactly the same thing, and even though it’s two lines of code, it’s much easier to understand.

> Beware of “clever” nuggets of code — they’re often confusing when others read the code later.

### Breaking Down Giant Statements

```jsx
var update_highlight = function (message_num) {
    if ($("#vote_value" + message_num).html() === "Up") {
        $("#thumbs_up" + message_num).addClass("highlighted");
        $("#thumbs_down" + message_num).removeClass("highlighted");
    } else if ($("#vote_value" + message_num).html() === "Down") {
        $("#thumbs_up" + message_num).removeClass("highlighted");
        $("#thumbs_down" + message_num).addClass("highlighted");
    } else {
        $("#thumbs_up" + message_num).removeClass("highighted"); 
        $("#thumbs_down" + message_num).removeClass("highlighted");
    }
};
```

Fortunately, a lot of the expressions are the same, which means we can extract them out as summary variables at the top of the function (this is also an instance of the DRY—Don’t Repeat Yourself—principle):

```jsx
let update_highlight = function (message_num) {
	let vote_value = $("#vote_value" + message_num).html();
	let thumbs_up = $("#thumbs_up" + message_num);
	let thumbs_down = $("#thumbs_down" + message_num);
	let hi = "highlighted";

	if (vote_value === "Up") {
        thumbs_up.addClass(hi);
        thumbs_down.removeClass(hi);
  } else if (vote_value === "Down") {
        thumbs_up.removeClass(hi);
        thumbs_down.addClass(hi);
  } else {
        thumbs_up.removeClass(hi);
        thumbs_down.removeClass(hi);
  }
}
```

### **Summary**

Giant expressions are hard to think about. This chapter showed a number of ways to break them down so the reader can digest them piece by piece.

One simple technique is to introduce “explaining variables” that capture the value of some large subexpression. This approach has three benefits:

- It breaks down a giant expression into pieces.
- It documents the code by describing the subexpression with a succinct name.
- It helps the reader identify the main “concepts” in the code.

Another technique is to manipulate your logic using De Morgan’s laws—this technique can sometimes rewrite a boolean expression in a cleaner way (e.g., `if (!(a && !b))` turns into `if (!a || b)`).

We showed an example where a complex logical condition was broken down into tiny statements like “`if (a < b) ...`”. In fact, *all* of the improved-code examples in this chapter had `if` statements with no more than *two* values inside them. This setup is ideal. It may not always seem possible to do this—sometimes it requires “negating” the problem or considering the opposite of your goal.

Finally, even though this chapter is about breaking down individual expressions, these same techniques often apply to larger blocks of code, too. So be aggressive in breaking down complex logic wherever you see it.
