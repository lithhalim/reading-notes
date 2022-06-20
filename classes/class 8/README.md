# Stack and Queue in JavaScript

## Stack
A stack normally is a structure of sequential and ordered elements and it’s based on the principle of last in first out (LIFO). You can argue that sometimes you can remove an element from the middle of the stack without actually removing the plate from the top, and that’s a valid argument. But sometimes this solution might not work as expected. Imagine that in some situations we only have the option to remove the most recent element added to the stack.

```javascript 
class Stack {
 constructor() {
   this.stack = [];
 }
 push(item) {
   this.stack.push(item);
 }
 pop() {
   this.stack.pop();
 }
}

```

Some developers like to implement stack data structures using linked lists instead of arrays in JavaScript. Although this might feel like a clever solution, the performance might not be the best one.

As Hyunjae Jun pointed out here, the performance of arrays instead of linked lists in stack data structures is better.

There are some specific cases where linked lists can perform better than arrays, but when implementing stack data structures in JavaScript, always prefer arrays. The array methods that you are going to use, push and pop, will have a time complexity of O(1), which means that they will run efficiently and will have the best performance possible.



# Queue
Imagine that we have a line of people to enter a specific restaurant. The last person to get in the line will be the last person to enter the restaurant, depending on the size of the line. The first person that got into line will be the first person to enter the restaurant.

```javascript 
function Queue() {
  this.queue = {};
  this.tail = 0;
  this.head = 0;
}

// Add an element to the end of the queue.
Queue.prototype.enqueue = function(element) {
  this.queue[this.tail++] = element;
}

// Delete the first element of the queue.
Queue.prototype.dequeue = function() {
  if (this.tail === this.head)
      return undefined

  var element = this.queue[this.head];
  delete element;
  return element;
}

```



Both stack and queue data structures are very flexible and easy to implement, but there are different use cases for each of them.

A stack is useful when we want to add elements inside a list into sequential order and remove the last element added. A queue is useful when we want the same behavior, but instead of removing the last added element, we want to remove the first element added to the list.

## Conclusion
Data structures are a very simple and powerful concept to learn about. They can help us improve our logical thinking, the way we structure and solve problems, and how we find the best solutions for specific use cases in our modern applications. Stacks and queues are two powerful solutions that can help us organize data into sequential order depending on what result we want to achieve and have efficient and fantastic performance.

