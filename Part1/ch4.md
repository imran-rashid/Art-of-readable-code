## Aesthetics

Good source code should be just as “easy on the eyes.” In this chapter, we’ll show how good use of spacing, alignment, and ordering can make your code easier to read.

Specifically, there are three principles we use:

- Use consistent layout, with patterns the reader can get used to.
- Make similar code look similar.
- Group related lines of code into blocks.

```cpp
class StatsKeeper {
    public:
    // A class for keeping track of a series of doubles
       void Add(double d);  // and methods for quick statistics about them
      private:   int count;        /* how many so far  */ 
      public:
        double Average();

    private:   
        double minimum;
        list<double>past_items;
        double maximum;
};
```

It would take you a lot longer to understand it than if you had this cleaner version instead:

```cpp
// A class for keeping track of a series of doubles
// and methods for quick statistics about them.
class StatsKeeper {
  public:
    void Add(double d);
    double Average();
 
  private:
    list<double> past_items;
    int count;  // how many so far

    double minimum;
    double maximum;
};
```

### Rearrange Line Breaks to Be Consistent and Compact

Suppose you were writing Java code to evaluate how your program behaves under various network connection speeds. You have a `TcpConnectionSimulator` that takes four parameters in the constructor:

1. The speed of the connection (Kbps)
2. The average latency (ms)
3. The “jitter” of the latency (ms)
4. The packet loss (percentage)

Your code needed three different `TcpConnectionSimulator` instances

```java
public class PerformanceTester {
    public static final TcpConnectionSimulator wifi = new TcpConnectionSimulator(
        500, /* Kbps */
        80, /* millisecs latency */
        200, /* jitter */
        1 /* packet loss % */);

    public static final TcpConnectionSimulator t3_fiber =
        new TcpConnectionSimulator(
            45000, /* Kbps */
            10, /* millisecs latency */
            0, /* jitter */
            0 /* packet loss % */);

    public static final TcpConnectionSimulator cell = new TcpConnectionSimulator(
        100, /* Kbps */
        400, /* millisecs latency */
        250, /* jitter */
        5 /* packet loss % */);
}
```

To make the code look more consistent, we could introduce extra line breaks

```java
public class PerformanceTester {
    public static final TcpConnectionSimulator wifi =
        new TcpConnectionSimulator(
            500,   /* Kbps */
            80,    /* millisecs latency */
            200,   /* jitter */
            1      /* packet loss % */);

    public static final TcpConnectionSimulator t3_fiber =
        new TcpConnectionSimulator(
            45000, /* Kbps */
            10,    /* millisecs latency */
            0,     /* jitter */
            0      /* packet loss % */);

    public static final TcpConnectionSimulator cell =
        new TcpConnectionSimulator(
            100,   /* Kbps */
            400,   /* millisecs latency */
            250,   /* jitter */
            5      /* packet loss % */);
}
```

Here’s a more compact way to write the class:

```java
public class PerformanceTester {
    // TcpConnectionSimulator(throughput, latency, jitter, packet_loss)
    //                            [Kbps]   [ms]    [ms]    [percent]

    public static final TcpConnectionSimulator wifi =
        new TcpConnectionSimulator(500,    80,     200,    1);

    public static final TcpConnectionSimulator t3_fiber =
        new TcpConnectionSimulator(45000,  10,     0,      0);

    public static final TcpConnectionSimulator cell =
        new TcpConnectionSimulator(100,    400,    250,    5);
}
```

### Use Methods to Clean Up Irregularity

Suppose you had a personnel database that provided the following function:

```sql
// Turn a partial_name like "Doug Adams" into "Mr. Douglas Adams".
// If not possible, 'error' is filled with an explanation.
stringExpandFullName(DatabaseConnection dc, string partial_name, string* error);
```

and this function was tested with a series of examples:

```sql
DatabaseConnection database_connection;
string error;
assert(ExpandFullName(database_connection, "Doug Adams", &error)
    == "Mr. Douglas Adams");
assert(error == "");
assert(ExpandFullName(database_connection, " Jake  Brown ", &error)
    == "Mr. Jacob Brown III");
assert(error == "");
assert(ExpandFullName(database_connection, "No Such Guy", &error) == "");
assert(error == "no match found");
assert(ExpandFullName(database_connection, "John", &error) == "");
assert(error == "more than one result");
```

To really improve this code, we need a helper method so the code can look like this:

```c
CheckFullName("Doug Adams", "Mr. Douglas Adams", "");
CheckFullName(" Jake  Brown ", "Mr. Jake Brown III", "");
CheckFullName("No Such Guy", "", "no match found");
CheckFullName("John", "", "more than one result");
```

Now it’s more clear that there are four tests happening, each with different parameters. Even though all the “dirty work” is inside `CheckFullName()`, that function isn’t so bad, either:

```cpp
void CheckFullName(string partial_name,
                   string expected_full_name,
                   string expected_error) {
    // database_connection is now a class member
    string error;
    string full_name = ExpandFullName(database_connection,partial_name, &error);
    assert(error ==expected_error);
    assert(full_name ==expected_full_name);
}
```

The moral of the story is that making code “look pretty” often results in more than just surface improvements—it might help you structure your code better.

### Use Column Alignment When Helpful

Sometimes you can introduce “column alignment” to make the code easier to read. For example, in the previous section, you could space out and line up the arguments to `CheckFullName()`:

```cpp
CheckFullName("Doug Adams"   , "Mr. Douglas Adams" , "");
CheckFullName(" Jake  Brown ", "Mr. Jake Brown III", "");
CheckFullName("No Such Guy"  , ""                  , "no match found");
CheckFullName("John"         , ""                  , "more than one result");
```

In this code, it’s easier to distinguish the second and third arguments to `CheckFullName()`.

### Pick a Meaningful Order, and Use It Consistently

There are many cases where the order of code doesn’t affect the correctness. For instance, these five variable definitions could be written in any order

```python
details  = request.POST.get('details')
location = request.POST.get('location')
phone    = request.POST.get('phone')
email    = request.POST.get('email')
url      = request.POST.get('url')
```

In situations like this, it’s helpful to put them in some meaningful order, not just random. Here are some ideas:

- Match the order of the variables to the order of the `<input>` fields on the corresponding HTML form.
- Order them from “most important” to “least important.”
- Order them alphabetically.

Whatever the order, you should use the same order throughout your code. It would be confusing to change the order later on

### Break Code into “Paragraphs”

Written text is broken into paragraphs for a number of reasons:

- It’s a way to group similar ideas together and set them apart from other ideas.
- It provides a visual “stepping stone”—without it, it’s easy to lose your place on the page.
- It facilitates navigation from one paragraph to another.

Code should be broken into “paragraphs” for the same reasons. For example, no one likes to read a giant lump of code like this:

```python
# Import the user's email contacts, and match them to users in our system.
# Then display a list of those users that he/she isn't already friends with.
def suggest_new_friends(user, email_password):
    friends = user.friends()
    friend_emails = set(f.email for f in friends)
    contacts = import_contacts(user.email, email_password)
    contact_emails = set(c.email for c in contacts)
    non_friend_emails = contact_emails - friend_emails
    suggested_friends = User.objects.select(email__in=non_friend_emails)
    display['user'] = user
    display['friends'] = friends
    display['suggested_friends'] = suggested_friends
    return render("suggested_friends.html", display)
```

It may not be obvious, but this function goes through a number of distinct steps. So it would be especially useful to break up those lines of code into paragraphs:

```python
def suggest_new_friends(user, email_password):
# Get the user's friends' email addresses.
    friends = user.friends()
    friend_emails = set(f.email for f in friends)

# Import all email addresses from this user's email account.
    contacts = import_contacts(user.email, email_password)
    contact_emails = set(c.email for c in contacts)

# Find matching users that they aren't already friends with.
    non_friend_emails = contact_emails - friend_emails
    suggested_friends = User.objects.select(email__in=non_friend_emails)

# Display these lists on the page.
    display['user'] = user
    display['friends'] = friends
    display['suggested_friends'] = suggested_friends

    return render("suggested_friends.html", display)
```

> Consistent style is more important than the “right” style.

### Summary

Everyone prefers to read code that’s aesthetically pleasing. By “formatting” your code in a consistent, meaningful way, you make it easier and faster to read.

Here are specific techniques we discussed:

- If multiple blocks of code are doing similar things, try to give them the same silhouette.
- Aligning parts of the code into “columns” can make code easy to skim through.
- If code mentions A, B, and C in one place, don’t say B, C, and A in another. Pick a meaningful order and stick with it.
- Use empty lines to break apart large blocks into logical “paragraphs.”
