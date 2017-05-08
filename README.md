# SimpleSet

A simple and fast implementation of a set in C++.  Other than the timing code,
this code base uses only basic types and the standard library, so is completely
portable to any platform.

## What about the `std::set` or `std::unordered_set`?

The implementations of the `std::unordered_set` that I've seen from GCC and
Clang both use a hash table of linked lists.  For use cases where insertions
are infrequent and lookups are frequent, this is an okay thing to do.  But if
insertions, deletions, and clears are common in your use case, then linked
lists incur too much dynamic allocation and deallocation to be performant.

## Pros and Cons

I have made this implementation that simply uses a hash table directly.  One
downside is that the default initialized element is used as an "empty" spot and
therefore cannot be represented in this kind of table.  For example, if you
store pointers, you cannot store a `nullptr`.  If you store integers, then you
cannot store the number zero.

Also of note, the size will not change from construction.  There is no dynamic
allocation, and the internal data array is entirely allocated on the stack
which may have all sorts of benefits such as easy cleanup and good caching
effects.

It is ideal to use small data types for this set class, such as integers or
pointers.  Otherwise, the amount of unused space will be enormous.

You are free to change any of the code.  Feel free to change things such as the
method of conflict resolution and size of the internal hash table.

## Interface

The interface is extremely simple.  If more interface functions are desired,
you can create an issue and I will try to be prompt about addressing and
implementing requested features.

The interface is currently:

- `bool insert(Key elem)`: Inserts the element if it is not already there.  If
  it was inserted, then `true` is returned.

```c++
std::unordered_set<int> set(1000);
int elem = 52;
bool was_contained = (set.find(elem) != set.end());
set.insert(elem);
```

Is equivalent to:

```c++
SimpleSet<int, 1000> set;
int elem = 52;
bool was_contained = !set.insert(elem);
```

So you can see the `insert()` function can actually equate to making two calls
to the `std::unordered_set` with negligible additional runtime costs.

- `bool contains(Key elem)`: Returns `true` if the element is in the set.

These are the only supported functions.  Not because the infrastructure cannot
handle more, but merely because the current uses for this class do not require
any more.  If more functionality is required by your use case, please create an
issue on GitHub.  Pull requests are very welcome.

## Results

Within `main.cpp`, you will see that there is some timing code there for
comparing the performance of my `SimpleSet` with both the `std::set` and the
`std::unordered_set`.  Here are the results when run on a virtual machine on my
personal laptop:

Class                | Time
-------------------- | --------
`SimpleSet`          |   382 ms
`std::unordered_set` | 1,697 ms
`std::set`           | 6,510 ms

This is for the version that is committed to the repository.  The number of
collisions for this data set is zero, which is sequential addresses of an array
of 25,000 64-bit unsigned integeres.  This is to check for contains twice and
inserting once each element of the 25,000 addresses, then clearing it.  And
repeat that process 100 times.

## Conclusion

It was fun making this class and seeing how optimal I could do in C++.  I was
surprisedThanks for giving it a try!
