# Heap

- What is a heap
```
Heap is a data structure is design for Heap sort.
It essentially represent an array as a complete binary tree.
```

![Heap Demo](https://www.cdn.geeksforgeeks.org/wp-content/uploads/binaryheap.png)

- Max and Min Heap
```
Max Heap: Each node is BIGGER than its two children.
Min Heap: Each node is SMALLER than its two children.
```

![Max and Min Heap](https://www.geeksforgeeks.org/wp-content/uploads/MinHeapAndMaxHeap.png)

- How to make a Max Heap
    - Start at `i = en / 2`
    - If Node_i's biggest child is bigger than Node_i, swap
    - decrease i until 0 
    
- How to make a Min Heap
    - Vice Versa
    

- Code Example
```javascript
let arr = [3, 8, 1, 2, 5, 7, 9];

function makeMaxHeap( arr, endPoint=null) {
    let currentIndex = Math.floor( arr.length / 2 );

    for(let i = currentIndex; i >= 0; i--) {
        bubbleChildren( arr, i, endPoint);
    }
}

function bubbleChildren(arr, index, endPoint=null) {
    if( endPoint === null ) endPoint = arr.length - 1;

    let maxIndex = index;
    let leftIndex = 2*index + 1;
    let rightIndex = 2*index + 2;

    if( arr[leftIndex] !== undefined && arr[leftIndex] > arr[maxIndex] && leftIndex <= endPoint ) {
        maxIndex = leftIndex;
    }

    if( arr[rightIndex] !== undefined && arr[rightIndex] > arr[maxIndex] && rightIndex <= endPoint ) {
        maxIndex = rightIndex;
    }

    if( maxIndex === index ) return;

    let tmp = arr[index];
    arr[index] = arr[maxIndex];
    arr[maxIndex] = tmp;

    bubbleChildren(arr, maxIndex, endPoint);
}
```

## How Heap sort works

Given a max heap, return a sorted array.
1. Swap the root node with the last node
    - root node will be the max number in the heap
    - set end point to arr length-1(root nod is done)
    
2. Make the heap a Max Heap
3. Repeat 1 and 2 until end point = 0

```javascript
function heapSort( maxHeap ) {
    let endPoint = maxHeap.length - 1;
    let tmp = null;

    while( endPoint !== 0 ) {
        tmp = maxHeap[0];
        maxHeap[0] = maxHeap[ endPoint ];
        maxHeap[endPoint] = tmp;
        endPoint --;

        makeMaxHeap(maxHeap, endPoint);
    }
}
```