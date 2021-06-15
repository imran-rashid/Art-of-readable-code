## Names That Can’t Be Misconstrued

In this chapter, we focus on a different topic: watching out for names that can be misunderstood.

> Actively scrutinize your names by asking yourself, “What other meanings could someone interpret from this name?”

Really try to be creative here, actively seeking “wrong interpretations.” This step will help you spot those ambiguous names so you can change them. 

### Example: Filter( )

You’re writing code to manipulate a set of database results

```jsx
results = Database.all_objects.filter("year <= 2011")
```

What does `results` now contain?

- Objects whose year is <= 2011?
- Objects whose year is *not* <= 2011?

The problem is that `filter` is an ambiguous word. It’s unclear whether it means “to pick out” or “to get rid of.” It’s best to avoid the name `filter` because it’s so easily misconstrued.

If you want “to pick out,” a better name is `select()`. If you want “to get rid of,” a better name is `exclude()`

### Example: Clip(text, length)

Suppose you have a function that clips the contents of a paragraph:

```python
# Cuts off the end of the text, and appends "..."
def Clip(text, length):
    ...
```

There are two ways you can imagine how `Clip()` behaves:

- It removes `length` from the end
- It truncates to a maximum `length`

The second way (truncation) is most likely, but you never know for sure. Rather than leave your reader with any nagging doubt, it would be better to name the function `Truncate(text, length)`.

However, the parameter name `length` is also to blame. If it were `max_length`, that would make it even more clear.

But we’re still not done. The name `max_length` still leaves multiple interpretations:

- A number of bytes
- A number of characters
- A number of words

As you saw in the previous chapter, this is a case where the units should be attached to the name. In this case, we mean “number of characters,” so instead of `max_length`, it should be `max_chars`

### Prefer min and max for (Inclusive) Limits

Let’s say your shopping cart application needs to stop people from buying more than 10 items at once:

```python
CART_TOO_BIG_LIMIT = 10

if shopping_cart.num_items() >= CART_TOO_BIG_LIMIT:
    Error("Too many items in cart.")
```

This code has a classic off-by-one bug. We could easily fix it by changing >= to >

(or by redefining CART_TOO_BIG_LIMIT to 11). But the root problem is that `CART_TOO_BIG_LIMIT` is an ambiguous name — it’s not clear whether you mean “up to” or “up to and including.”

> The clearest way to name a limit is to put max_ or min_ in front of the thing being limited.

```python
MAX_ITEMS_IN_CART = 10

if shopping_cart.num_items() > MAX_ITEMS_IN_CART:
    Error("Too many items in cart.")
```

### Prefer first and last for Inclusive Ranges

Here is another example where you can’t tell if it’s “up to” or “up to and including”

```python
print integer_range(start=2, stop=4)
# Does this print [2,3] or [2,3,4] (or something else)?
```

For inclusive ranges likes these (where the range should include both end points), a good choice is `first/last`. For instance:

```
set.PrintKeys(first="Bart", last="Maggie")
```

In addition to `first/last`, the names `min/max` may also work for inclusive ranges, assuming they “sound right” in that context.

### Prefer begin and end for Inclusive/Exclusive Ranges

In practice, it’s often more convenient to use inclusive/exclusive ranges. For example, if you want to print all the events that happened on October 16, it’s easier to write:

```python
PrintEventsInRange("OCT 16 12:00am", "OCT 17 12:00am")
```

than it is to write:

```python
PrintEventsInRange("OCT 16 12:00am", "OCT 16 11:59:59.9999pm")
```

So what is a good pair of names for these parameters? Well, the typical programming convention for naming an inclusive/exclusive range is `begin/end`.

### Naming Booleans

Here’s a dangerous example:

```cpp
bool read_password = true;
```

Depending on how you read it, there are two very different interpretations:

- We *need* to read the password
- The password has already *been* read

In this case, it’s best to avoid the word “read,” and name it `need_password` or `user_is_authenticated` instead.

In general, adding words like `is`, `has`, `can`, or `should` can make booleans more clear

Finally, it’s best to avoid negated terms in a name. For example, instead of:

```cpp
bool disable_ssl = false;

// it would be easier to read (and more compact) to say
bool use_ssl = true;
```

Some names are misleading because the user has a preconceived idea of what the name means, even though you mean something else. In these cases, it’s best to just “give in” and change the name so that it’s not misleading.

### Example: get*( )

Many programmers are used to the convention that methods starting with get are “lightweight accessors” that simply return an internal member. Going against this convention is likely to mislead those users.

```java
public class StatisticsCollector {
    public void addSample(double x) { ... }

    public double getMean() {
        // Iterate through all samples and return total / num_samples
    }
    ...
}
```

In this case, the implementation of `getMean()` is to iterate over past data and calculate the mean on the fly. This step might be very expensive if there’s a lot of data! But an unsuspecting programmer might call `getMean()` carelessly, assuming that it’s an inexpensive call.

Instead, the method should be renamed to something like `computeMean()`, which sounds more like an expensive operation.

### Summary

The best names are ones that can’t be misconstrued—the person reading your code will understand it the way you meant it, and no other way. Unfortunately, a lot of English words are ambiguous when it comes to programming, such as `filter`, `length`, and `limit`.

Before you decide on a name, play devil’s advocate and imagine how your name might be misunderstood. The best names are resistant to misinterpretation.

When it comes to defining an upper or lower limit for a value, `max_` and `min_` are good prefixes to use. For inclusive ranges, `first` and `last` are good. For inclusive/exclusive ranges, `begin` and `end` are best because they’re the most idiomatic.

When naming a boolean, use words like `is` and `has` to make it clear that it’s a boolean. Avoid negated terms (e.g., `disable_ssl`).

Beware of users’ expectations about certain words. For example, users may expect `get()` or `size()` to be lightweight methods.
