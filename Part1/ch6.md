## Making Comments Precise and Compact

If you’re going to write a comment at all, it might as well be precise—as specific and detailed as possible. On the other hand, comments take up extra space on the screen, and take extra time to read. So comments should also be compact

> Comments should have a high information-to-space ratio.

### Keep Comments Compact

Here’s an example of a comment for a C++ type definition:

```cpp
// The int is the CategoryType.
// The first float in the inner pair is the 'score',
// the second is the 'weight'.
typedef hash_map<int, pair<float, float> > ScoreMap;
```

But why use three lines to explain it, when you can illustrate it in just one line?

```cpp
// CategoryType -> (score, weight)
typedef hash_map<int, pair<float, float> > ScoreMap;
```

### Avoid Ambiguous Pronouns

It takes extra work for the reader to “resolve” a pronoun. And in some cases, it’s unclear what “it” or “this” is referring to. Here’s an example:

```
// Insert the data into the cache, but check if it's too big first.
```

In this comment, “it” might refer to the data or the cache. You could probably figure that out by reading the rest of the code. But if you have to do that, what’s the point of the comment?

The safest thing is to “fill in” pronouns if there’s any chance of confusion. In the previous example, let’s assume “it” was “the data”:

```
// Insert the data into the cache, but check if the data is too big first.
```

This is the simplest change to make. You could also have restructured the sentence to make “it” perfectly clear:

```
// If the data is small enough, insert it into the cache.
```

### Polish Sloppy Sentences

```python
# Depending on whether we've already crawled this URL before, 
# give it a different priority.
```

This sentence might seem okay, but compare it to this version:

```python
# Give higher priority to URLs we've never crawled before.
```

### Describe Function Behavior Precisely

Imagine you just wrote a function that counts the number of lines in a file:

```cpp
// Return the number of lines in this file.
int CountLines(string filename) { ... }
```

This comment isn’t very precise—there are a lot of ways to define a “line.” Here are some corner cases to think about:

- `""` (an empty file)—0 or 1 line?
- `"hello"`—0 or 1 line?
- `"hello\n"`—1 or 2 lines?
- `"hello\n world"`—1 or 2 lines?
- `"hello\n\r cruel\n world\r"`—2, 3, or 4 lines?

The simplest implementation is to count the number of newline (`\n`) characters. (This is the way the Unix command `wc` works.) Here’s a better comment to match this implementation:

```cpp
// Count how many newline bytes ('\n') are in the file.
int CountLines(string filename) { ... }
```

### State the Intent of Your Code

As we mentioned in the previous chapter, commenting is often about telling the reader what you were thinking about when you wrote the code. Unfortunately, many comments end up just describing what the code does in literal terms, without adding much new information.

Here’s an example of such a comment:

```cpp
void DisplayProducts(list<Product> products) {
    products.sort(CompareProductByPrice);

 // Iterate through the list in reverse order
    for (list<Product>::reverse_iterator it = products.rbegin(); it != products.rend();
         ++it)
        DisplayPrice(it->price);

    ...
}
```

All this comment does is just describe the line below it. Instead, consider this better comment:

```cpp
    // Display each price, from highest to lowest
    for (list<Product>::reverse_iterator it = products.rbegin(); ... )
```

This comment explains what the program is doing at a higher level. This is much more in tune with what the programmer was thinking when she wrote the code.

### “Named Function Parameter” Comments

Suppose you saw a function call like this one:

```objectivec
Connect(10, false);
```

This function call is a bit mysterious because of those integer and boolean literals being passed in.

In languages like Python, you can assign the arguments by name:

```python
def Connect(timeout, use_encryption):  ...

# Call the function using named parameters
Connect(timeout = 10,use_encryption = False)
```

In languages like C++ and Java, you can’t do this. However, you can use an inline comment to the same effect:

```c
void Connect(int timeout, bool use_encryption) { ... }

// Call the function with commented parameters
Connect(/* timeout_ms = */ 10,/* use_encryption = */ false);
```

```objectivec
// Don't do this!
Connect( ... , false /* use_encryption */);

// Don't do this either!
Connect( ... , false /* = use_encryption */);
```

### Use Information-Dense Words

Once you’ve been programming for a number of years, you notice that the same general problems and solutions come up repeatedly. Often, there are specific words or phrases that have been developed to describe these patterns/idioms. Using these words can make your comments much more compact.

For example, suppose your comment were:

```php
// This class contains a number of members that store the same information as in the
// database, but are stored here for speed. When this class is read from later, those
// members are checked first to see if they exist, and if so are returned; otherwise the
// database is read from and that data stored in those fields for next time.
```

Instead, you could just say

```php
// This class acts as a ***caching layer*** to the database.
```

As another example, a comment such as:

```kotlin
// Remove excess whitespace from the street address, and do lots of other cleanup
// like turn "Avenue" into "Ave." This way, if there are two different street addresses
// that are typed in slightly differently, they will have the same cleaned-up version and
// we can detect that these are equal.
```

could instead be:

```kotlin
// ***Canonicalize*** the street address (remove extra spaces, "Avenue" -> "Ave.", etc.)
```

There are lots of words and phrases that pack a lot of meaning, such as “heuristic,” “brute-force,” “naive solution,” and the like. If you have a comment that feels a bit long-winded, see if it can be described as a typical programming situation.

### **Summary**

This chapter is about writing comments that pack as much information into as small a space as possible. Here are the specific tips:

- Avoid pronouns like “it” and “this” when they can refer to multiple things.
- Describe a function’s behavior with as much precision as is practical.
- Illustrate your comments with carefully chosen input/output examples.
- State the high-level intent of your code, rather than the obvious details.
- Use inline comments (e.g., `Function(/* arg = */ ... )` ) to explain mysterious function arguments.
- Keep your comments brief by using words that pack a lot of meaning.
