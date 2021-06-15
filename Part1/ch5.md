## Knowing What to Comment

The goal of this chapter is to help you realize what you should be commenting. You might think the purpose of commenting is to “explain what the code does,” but that is just a small part of it.

> The purpose of commenting is to help the reader know as much as the writer did.

When you’re writing code, you have a lot of valuable information in your head. When other people read your code, that information is lost—all they have is the code in front of them.

### What NOT to Comment

All of the comments in this code are worthless:

```cpp
// The class definition for Account
class Account {
  public:
// Constructor
    Account();

// Set the profit member to a new value
    void SetProfit(double profit);

// Return the profit from this Account
    double GetProfit();
};
```

These comments are worthless because they don’t provide any new information or help the reader understand the code better.

> Don’t comment on facts that can be derived quickly from the code itself.

The word “quickly” is an important distinction, though. Consider the comment for this Python code:

```python
# remove everything after the second '*'
name = '*'.join(line.split('*')[:2])
```

Technically, this comment doesn’t present any “new information” either. If you look at the code itself, you’ll eventually figure out what it’s doing. But for most programmers, reading the commented code is much faster than understanding the code without it.

### Don’t Comment Just for the Sake of Commenting

Some professors require their students to have a comment for each function in their homework code. As a result, some programmers feel guilty about leaving a function naked without comments and end up rewriting the function’s name and arguments in sentence form:

```cpp
// Find the Node in the given subtree, with the given name, using the given depth.
Node* FindNodeInSubtree(Node* subtree, string name, int depth);
```

If you want to have a comment here, it might as well elaborate on more important details:

```cpp
// Find a Node with the given 'name' or return NULL.
// If depth <= 0, only 'subtree' is inspected.
// If depth == N, only 'subtree' and N levels below are inspected.
Node* FindNodeInSubtree(Node* subtree, string name, int depth);
```

### Don’t Comment Bad Names—Fix the Names Instead

A comment shouldn’t have to make up for a bad name. For example, here’s an innocent-looking comment for a function named `CleanReply()`:

```cpp
// Enforce limits on the Reply as stated in the Request,// such as the number of items returned, or total byte size, etc.
void CleanReply(Request request, Reply reply);
```

Most of the comment is simply explaining what “clean” means. Instead, the phrase “enforce limits” should be moved into the function name:

```cpp
// Make sure 'reply' meets the count/byte/etc. limits from the 'request'
voidEnforceLimitsFromRequest(Request request, Reply reply);
```

This function name is more “self-documenting.” A good name is better than a good comment because it will be seen everywhere the function is used.

Here is another example of a comment for a poorly named function:

```cpp
// Releases the handle for this key. This doesn't modify the actual registry.
void DeleteRegistry(RegistryKey* key);
```

The name `DeleteRegistry()` sounds like a dangerous function (it *deletes* the registry?!). The comment “This doesn’t modify the actual registry” is trying to clear up the confusion.

Instead, we could use a more self-documenting name like:

```cpp
voidReleaseRegistryHandle(RegistryKey* key);
```

In general, you don’t want “crutch comments”—comments that are trying to make up for the unreadability of the code. Coders often state this rule as **good code > bad code + good comments.**

### Recording Your Thoughts

you should include comments to record valuable insights about the code.

Here’s an example:

```java
// Surprisingly, a binary tree was 40% faster than a hash table for this data.
// The cost of computing a hash was more than the left/right comparisons.
```

This comment teaches the reader something and stops any would-be optimizer from wasting their time.

Here’s another example:

```cpp
// This heuristic might miss a few words. That's OK; solving this 100% is hard.
```

Without this comment, the reader might think there’s a bug and might waste time trying to come up with test cases that make it fail, or go off and try to fix the bug.

A comment can also explain why the code isn’t in great shape:

```cpp
// This class is getting messy. Maybe we should create a 'ResourceNode' subclass to
// help organize things.
```

This comment acknowledges that the code is messy but also encourages the next person to fix it (with specifics on how to get started). Without the comment, many readers would be intimidated by the messy code and afraid to touch it.

### Comment on Your Constants

When defining a constant, there is often a “story” for what that constant does or why it has that specific value. For example, you might see this constant in your code:

```python
NUM_THREADS = 8
```

It may not seem like this line needs a comment, but it’s likely that the programmer who chose it knows more about it:

```python
NUM_THREADS = 8# as long as it's >= 2 * num_processors, that's good enough.
```

Now the person reading the code has some guidance on how to adjust that value (e.g., setting it to 1 is probably too low, and setting it to 50 is overkill).

Or sometimes the exact value of a constant isn’t important at all. A comment to this effect is still useful:

```
// Impose a reasonable limit - no human can read that much anyway.
const int MAX_RSS_SUBSCRIPTIONS = 1000;
```

Sometimes it’s a value that’s been highly tuned, and probably shouldn’t be adjusted much:

```
image_quality = 0.72;// users thought 0.72 gave the best size/quality tradeoff
```

In all these examples, you might not have thought to add comments, but they’re quite helpful.

### Put Yourself in the Reader’s Shoes

When someone else reads your code, there are parts likely to make them think, Huh? What’s this all about? Your job is to comment those parts.

For example, take a look at the definition of `Clear()`:

```cpp
struct Recorder {
    vector<float> data;
    ...
    void Clear() {
vector<float>().swap(data);  //Huh? Why not just data.clear()?
    }
};
```

When most C++ programmers see this code, they think, *Why didn’t he just do `data.clear()` instead of swapping with an empty vector?* Well, it turns out that this is the only way to force a `vector` to truly relinquish its memory to the memory allocator. It’s a C++ detail that isn’t well known. The bottom line is that it should be commented:

```cpp
// Force vector to relinquish its memory (look up "STL swap trick")
vector<float>().swap(data);
```

### **Summary**

The purpose of a comment is to help the reader know what the writer knew when writing the code. This whole chapter is about realizing all the not-so-obvious nuggets of information you have about the code and writing those down.

What *not* to comment:

- Facts that can be quickly derived from the code itself.
- “Crutch comments” that make up for bad code (such as a bad function name)—fix the code instead.

Thoughts you should be recording include:

- Insights about why code is one way and not another (“director commentary”).
- Flaws in your code, by using markers like TODO: or XXX:.
- The “story” for how a constant got its value.

Put yourself in the reader’s shoes:

- Anticipate which parts of your code will make readers say “Huh?” and comment those.
- Document any surprising behavior an average reader wouldn’t expect.
- Use “big picture” comments at the file/class level to explain how all the pieces fit together.
- Summarize blocks of code with comments so that the reader doesn’t get lost in the details.
