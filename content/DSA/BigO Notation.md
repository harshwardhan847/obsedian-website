---
title: BigO Notation
sub-title: To find time complexity of an algorithm
tags:
  - dsa
  - BigO
  - complexity
  - time_complexity
  - space
  - space_complexity
  - time
draft: false
---
# Big O
Big O notation helps us understand how long an algorithm will take to run or how much memory it will need as the amount of data it handles grows.

## O(n)

> Signifies that the execution time of algorithm grows linearly in proportion to the size of the input data (n)

n + n = 2n -> O(2n) // drop the constants so it becomes O(n) 

## O(1)

> O(1) aka constant time, signifies that the execution time of an algorithm remains constant regardless of the input size

## O(n^2)

> Indicates that the algorithm's execution time grows quadratically with the size of the input data ( represented by n).

###### loop inside loop
```javascript
for (let i = 0; i<arr.length; i++){
	for (let j=i+1; j<arr.length; j++){
		console.log(`Pair: ${arr[i]}, ${[j]}`);
	}
}
```

n^2 + n -> O(n^2 + n) -> O(n^2) 


## O(log n)

> O(log n) time complexity refers to an algorithm's runtime that grows logarithmically with the size of the input. In simpler terms, as the input size increases, the time it takes for the algorithm to run increases slowly.

log<sub>2</sub>8 = ?
2 to the what power equals 8?
log<sub>2</sub>8 = 3

O(log n)