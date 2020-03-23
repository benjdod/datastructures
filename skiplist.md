## Understanding Skip Lists

Let's imagine we were to use a linked list to store data. If we wanted to access an entry in that list, we would have to start at the beginning and jump from one node to the next until we found our value. This operation is pretty quick if your value is towards the front of the list, but if it's at the back, the whole thing gets pretty expensive pretty quick. 
As we know, the time complexity of traversing a linked list is O(n).

What a skip list does is shorten the path to a value by decreasing the number of jumps we have to make to get there. Instead of having just one pointer for each data value in the list, it has multiple, which point to different spots along the list. 

For illustration, let's imagine we have a series of values in a linked list:

![A simple linked list: 2 5 17 32 41 60 91 99](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_linked_simple.svg)

What if we wanted to see if the list contains the value 60? In that case, we would have to jump from 2 to 5 to 17 and so on until we either found 60 (60 is in the list), or a number greater than 60, or the end of the list (60 isn't in the list). 

![Traversing the above mentioned list until we get to 60...](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_linked_traverse.svg)

But we can do better. let's add some new pointers to our structure:

![A modified version of the list mentioned above, but with another layer of pointers](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_mp_simple.svg)

Now, some values have multiple pointers associated with them as opposed to just one. If we wanted to access 60, we could use these new pointers as shortcuts. So, starting at 2, we could test 32 and jump straight there instead of going through 5 and 17 as well. In this case, we could access 60 with only two jumps as opposed to five.

![Traversing the modified list to find 60](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_mp_traverse1.svg)

In this way, we can use these pointers to access any value in the list. If we wanted to access 91, we would jump to 32 on the top row. then after finding out that 99 is greater than our desired value, we would descend a level and proceed there. 

![Traversing the modified list to find 91](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_mp_traverse1.svg)

In a nutshell, this is the idea behind a skip list. Each value has a certain number of pointers, which make up different levels which we can use to move through the list. 

![A small skip list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_sl_simple.svg)

The higher a level is, the less "pointer-dense" it is. So when we traverse, we begin jumping in the highest level where we can cover the most ground per jump, descend a level when we reach an impasse, and repeat.

![Traversing a skip list](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_sl_traverse1.svg)

Now why is a skiplist called probabilistic? Hopefully not becase it only has a chance of actually working. The reason is that we leave determining the number of pointers for a value to the hands of chance. The way this is done with skip lists is to do the computer version of flipping a coin until we get a tails and counting how many times we flipped it.

```
int n = 0;
while (randbool()) {
    n++;
}
```

Using this probabilistic method, the time complexity for traversing a skip list is **O(log n)**.
