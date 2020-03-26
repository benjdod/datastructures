## Understanding Skip Lists

Let's imagine we were to use a sorted linked list to store data. If we wanted to access an entry in that list, we would have to start at the beginning and jump from one node to the next until we found our item. This operation is pretty quick if your item is towards the front of the list, but if it's at the back, the whole thing gets pretty expensive pretty quick. As we know, the time complexity of traversing a linked list is O(n), which could be less than ideal for some uses. 

A skip list is essentially an elaboration on the idea of a linked list which allows for quicker traversal. It does this by decreasing the number of jumps we have to make to get there. Instead of having just one pointer for each data item in the list, it has multiple, which point to different spots along the list. 

For illustration, let's imagine we have a series of items in a linked list:

![A simple linked list: 2 5 17 32 41 60 91 99](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/linked_simple.svg)

What if we wanted to traverse to the item 60? In that case, we would have to jump from 2 to 5 to 17 and so on until we find it.

![Traversing the above mentioned list until we get to 60...](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/linked_traverse.svg)

But we can do better. let's add some new pointers to our structure:

![A modified version of the list mentioned above, but with another layer of pointers](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/mp_simple.svg)

Now, some items have multiple pointers associated with them as opposed to just one. If we wanted to access 60, we could use these new pointers as shortcuts. So, starting at 2, we could test 32 and jump straight there instead of going through 5 and 17 as well. In this case, we could access 60 with only two jumps as opposed to five.

![Traversing the modified list to find 60](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/mp_traverse1.svg)

In this way, we can use these pointers to access any item in the list. If we wanted to access 91, we would jump to 32 on the top row. then after finding out that 99 is greater than our desired item, we would descend a level and proceed there. 

![Traversing the modified list to find 91](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/mp_traverse1.svg)

In a nutshell, this is the idea behind a skip list. A skip list has O(log(n)) levels of pointers, each level having half the number of pointers than the previous level. Thus, the higher a level is, the less "pointer-dense" it is. So when we traverse, we begin jumping in the highest level where we can cover the most ground per jump, descend a level when we reach an impasse, and repeat.

![Traversing a skip list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/sl_traverse1.svg)

As mentioned, in a perfect skip list, each level has half as many pointers as the one below it, and the items are sorted. Thus, if perfectly arranged, it guarantees an access time of *O(log(n))*. 

***But why O(log(n)) for everything?***

In the ideal skip list, each level has exactly half as many pointers as the one below it. For a list with *n* elements, level 0 would have n pointers, level 1 would have n/2 pointers, level 2 would have n/4 pointers, and so on. In visual terms, we'd expect something like:

![Vertical lines representing the pointer stacks in an ideal skip list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/sl_pointers_blank.svg)

What would be the worst access we could want to do on this structure? Clearly it would be the element right before the end. So if we make that access, we get:

![Accessing the next to last pointer stack in the above list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/sl_pointers_worstaccess16.svg)

Where *log<sub>2</sub>(16) = 4*. If we use a subset of this structure, we still see the same logarithmic behavior.

![Accessing the next to last pointer stack in half of the above list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/sl_pointers_worstaccess8.svg)

Of course, these logarithmic access times require perfect list structure, which could necessitate complete list rearrangement on an insertion.

So instead, skip lists use randomization to determine the number of pointers for each element. If you flip a coin, the probability that it will be heads is 1/2, as is the probability for tails. Flip it again, and the probability of getting two heads in a row is 1/4. Keep flipping until you get a tails, and then count how many times you flipped. If you use this strategy to determine the height of all your elements' pointer stacks, you'll get around 1/2 of them with two levels, 1/4 of them with three levels, and so on.

```
int n = 1;            start with one level
while (randbool()) {
    n++;              and then flip the coin
}
```

This strategy will give you about the same *O(log(n))* performance in access as if you managed the structure of the list with an authoritative hand, but with much better performance for insertion. 

To understand that, let's consider it a different way. Consider that you want to go backwards from an element in a list to the root node and count the number of steps you have to take. When travelling backwards, you always want to go up as high as possible in the current node, as that's how you would have entered it. Since each subsequent level has half the number of pointers, the probability that you can move up a level in any given node is 1/2, and the probability that you can't is also 1/2. So if you want to move up *i* levels, you can sum up the number of steps required with a cool little formula:


&nbsp;&nbsp;&nbsp;&nbsp;*C(i) = 1 + 0.5*C(i) + 0.5*C(i-1)* 

Doubling it gives us:

&nbsp;&nbsp;&nbsp;&nbsp;*2C(i) = 2 + C(i) + C(i-1)* 

Which can be reduced to:

&nbsp;&nbsp;&nbsp;&nbsp;*C(i) = 2 + C(i-1)* 

Notice that the formula is recursive, which means we can expand it out. We know that once we hit 0 levels to climb, we're done recursing. In math terms, this means *C(0) = 0* So expanding gives us:

&nbsp;&nbsp;&nbsp;&nbsp;*C(i) = 2 + C(i-1)* <br>
&nbsp;&nbsp;&nbsp;&nbsp;*C(i) = 2 + (2 + C(i-2))* <br>
&nbsp;&nbsp;&nbsp;&nbsp;...<br>
&nbsp;&nbsp;&nbsp;&nbsp;*C(i) = 2 + (2 + (2 + ( ... ( 2 + C(0))) ... ))* <br>
&nbsp;&nbsp;&nbsp;&nbsp;*C(i) = 2i* <br>

Since the list has *O(log(n))* levels, the time complexity for access is also *O(log(n))*.

Of course, this access time is not 100% guaranteed. If our coin-flipping mechanism were to just flip tails every time, we'd get one big linked list in the bottom layer and a time complexity of *O(n)* But the likelihood of this actually happening gets lower and lower as more elements are added, and eventually becomes so small that we don't have to worry about it.

&nbsp;&nbsp;&nbsp;&nbsp;*0.5 \* 0.5 \* 0.5 \* ... = some crazy small number* <br>

### Tradeoffs

The most obvious comparison to make is to its simpler cousin, the linked list. For all operations, the skip list tends towards *(O(log(n))* in the average case, but since the layout of the pointer layers is randomly determined, it is not impossible for a skip list to have times worse than that.

|Operation|Linked List|Skip List (avg)|Skip List (worst)|
|----|----|----|----|
|Access|O(n)|O(log(n))|O(n)|
|Search|O(n)|O(log(n))|O(n)|
|Insert|O(1)|O(log(n))|O(n)|
|Delete|O(1)|O(log(n))|O(n)|

The place where skip lists perform worse than linked lists is in memory usage. Since each item has one *or more* pointers, the average space complexity must grow at a rate higher than *O(n)*. Skip lists have a space complexity of *O(n log(n))*.