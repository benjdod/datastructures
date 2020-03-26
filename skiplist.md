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

*But why O(log(n)) for everything?*

In the ideal skip list, each level has exactly half as many pointers as the one below it. For a list with *n* elements, level 0 would have n pointers, level 1 would have n/2 pointers, level 2 would have n/4 pointers, and so on. In visual terms, we'd expect something like:

![Vertical lines representing the pointer stacks in an ideal skip list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/sl_pointers_blank.svg)

What would be the worst access we could want to do on this structure? Clearly it would be the element right before the end. So if we make that access, we get:

![Accessing the next to last pointer stack in the above list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist/sl_pointers_worstaccess.svg)

Why is a skiplist called probabilistic? Hopefully not becase it only has a chance of actually working. The reason is that we leave determining the number of pointers for a item to the hands of chance. The way this is done with skip lists is to do the computer version of flipping a coin until we get a tails and counting how many times we flipped it.

```
int n = 0;
while (randbool()) {
    n++;
}
```

Using this method of generating skip lists, the time complexity for accessing stays near *O(log(n))*. Of course, this is not guaranteed. If our coin-flipping mechanism were to immediately flip tails every time and give us one big linked list in the bottom layer, our time complexity would be O(n), but the likelihood of getting this "degenerate case" is so low as to be practically impossible. 

### Tradeoffs

The most obvious comparison to make is to its simpler cousin, the linked list. For all operations, the skip list tends towards *(O(log(n))* in the average case, but since the layout of the pointer layers is randomly determined, it is not impossible for a skip list to have times worse than that.

|Operation|Linked List|Skip List (avg)|Skip List (worst)|
|----|----|----|----|
|Access|O(n)|O(log(n))|O(n)|
|Search|O(n)|O(log(n))|O(n)|
|Insert|O(1)|O(log(n))|O(n)|
|Delete|O(1)|O(log(n))|O(n)|

The place where skip lists perform worse than linked lists is in memory usage. Since each item has one *or more* pointers, the average space complexity must grow at a rate higher than *O(n)*. Skip lists have a space complexity of *O(n log(n))*.