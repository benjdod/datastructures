## Understanding Skip Lists

Let's imagine we were to use a linked list to store data. If we wanted to access an entry in that list, we would have to start at the beginning and jump from one node to the next until we found our value. This operation is pretty quick if your value is towards the front of the list, but if it's at the back, the whole thing gets pretty expensive pretty quick. 
As our desired element nears the back of the list, the time it takes gets nearer and nearer to being proportional to the length of the list. In the worst case, accessing the last element, the time it would take would be exactly proportional, O(n).

What a skip list does is shorten the path to a value by decreasing the number of jumps we have to make to get there. 

For illustration, let's imagine we have a series of values in a list:

![Linked List simple](https://github.com/benjdod/datastructures/blob/master/resources/skiplist_linked_simple.svg)

Let's imagine we wanted to see if the list contains the value 141. In that case, we would have to jump from 2 to 5 to 17 and so on until we either found 141 (141 is in the list) or a number greater than 141 (141 isn't in the list). 

But, let's imagine we add some new pointers to our structure:

2      32    141
2 5 17 32 98 141 8056

Now, if we wanted to access 141, we could use these shortcuts. So, starting at 2, we could test 32 and jump straight there instead of going through 5 and 17 as well. In this way, we could access 141 with only two comparisons as opposed to five.

We can use these pointers to access any value in the list. If we wanted to access 98, we would jump to 32 on the top row, then after finding out that 141 is greater, we would descend a level and proceed there. 
