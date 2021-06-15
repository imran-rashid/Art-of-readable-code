## Code Should Be Easy to Understand

Over the past five years, we have collected hundreds of examples of “bad code” (much of it our own), and analyzed what made it bad, 
and what principles/techniques were used to make it better. What we noticed is that all of the principles stem from a single theme.

> Code should be easy to understand.

### What makes code "Better"?

Most programmers make programming decisions based on gut feel and intuition.

We all know that code like this →

```cpp
for (Node* node = list->head; node != NULL; node = node->next) {
	cout << node->data;
}
```

Better to write like this

```cpp
Node* node = list->head;

if (node == NULL) {
	return;
}

while (node->next != NULL) {
	cout << node->data;
	node = node->next;
}

if (node != NULL) {
	cout << node->data;
}
```

But a lot of times, it’s a tougher choice. For example

```cpp
// 1st version
return exponent >= 0 ? mantissa * (1 << exponent) : mantissa / (1 << -exponent);

// 2nd version
if (exponent >= 0) {
	return mantissa * (1 << exponent);
} else {
	return mantissa / (1 << -exponent);
}
```

The first version is more compact, but the second version is less intimidating. Which criterion is more important? In general, how do you decide which way to code something?

Here comes in FUNDAMENTAL THEOREM

> Code should be written to minimize the time it would take for someone else to understand it.

What do we mean by this? Quite literally, if you were to take a typical colleague of yours, and measure how much time it took him to read through 
your code and understand it, this “time-till-understanding” is the theoretical metric you want to minimize.

### Is Smaller Always Better?

Generally speaking, the less code you write to solve a problem, the better.

But fewer lines isn’t always better! There are plenty of times when a one-line expression like

```cpp
assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```

takes more time to understand than if it were two lines

```cpp
bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket->IsOccupied());
```

So even though having fewer lines of code is a good goal, minimizing the time-till-understanding is an even better goal

### Does Time-Till-Understanding Conflict with Other Goals?

You might be thinking, What about other constraints, like making *code efficient, or well-architected, or easy to test, and so on? Don’t these 
sometimes conflict with wanting to make code easy to understand?* 

We’ve found that these other goals don't interfere much at all. Even in the realm of highly optimized code, there are still ways to make 
it highly readable as well. And making your code easy to understand often leads to code that is well architected and easy to test.

### The Hard Part

Yes, it requires extra work to constantly think about whether an imaginary outsider would find your code easy to understand. 
Doing so requires turning on a part of your brain that might not have been on while coding before
