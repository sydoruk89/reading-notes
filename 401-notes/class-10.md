# [Stacks and Queues](https://codefellows.github.io/common_curriculum/data_structures_and_algorithms/Code_401/class-10/resources/stacks_and_queues.html)
## Stacks
FILO
First In Last Out
LIFO
Last In First Out
Push O(1)
ALOGORITHM push(value)
// INPUT <-- value to add, wrapped in Node internally
// OUTPUT <-- none
   node = new Node(value)
   node.next <-- Top
   top <-- NodePop O(1)ALGORITHM pop()
// INPUT <-- No input
// OUTPUT <-- value of top Node in stack
// EXCEPTION if stack is empty
   Node temp <-- top
   top <-- top.next
   temp.next <-- null
   return temp.valuePeek O(1)ALGORITHM peek()
// INPUT <-- none
// OUTPUT <-- value of top Node in stack
// EXCEPTION if stack is empty
   return top.valueALGORITHM isEmpty()
// INPUT <-- none
// OUTPUT <-- boolean
return top = NULL
## Queue
FIFOFirst In First Out
LILOLast In Last Out
Enqueue O(1)
When you add an item to a queue, you use the enqueue action.
ALGORITHM enqueue(value)
// INPUT <-- value to add to queue (will be wrapped in Node internally)
// OUTPUT <-- none
   node = new Node(value)
   rear.next <-- node
   rear <-- node
   Dequeue O(1)
   When you remove an item from a queue, you use the dequeue action. 
   ALGORITHM dequeue()
// INPUT <-- none
// OUTPUT <-- value of the removed Node
// EXCEPTION if queue is empty

   Node temp <-- front
   front <-- front.next
   temp.next <-- null

   return temp.value
   Peek O(1)
   ALGORITHM peek()
// INPUT <-- none
// OUTPUT <-- value of the front Node in Queue
// EXCEPTION if Queue is empty

   return front.valueIsEmpty O(1)ALGORITHM isEmpty()
// INPUT <-- none
// OUTPUT <-- boolean

return front = NULL