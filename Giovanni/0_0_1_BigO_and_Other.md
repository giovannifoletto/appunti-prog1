 # BIG-O NOTATIONS AND ALGORITHM EFFICIENCY:

**SOURCE** : [link](https://www.youtube.com/watch?v=__vX2sjlpXU)

Is simplified analysis of an algorithm's efficiency:

1. complexity in terms of input size (N)
2. Machine-indipendent
3. uses basic computer steps to calc that
4. keep track of time and space

We can examine:

* worst-case (usually used with Big-O notations)
* average-case
* best-case

BIg-O Notations:

* ignore costants:
  $$
  5n \rightarrow O(n)
  $$

* certain terms 'dominate' (like small-O, but reverse):
  $$
  O(1) < O(logn) < O(n) < O(nlogn) < O(n^2) < O(2^n) < O(n!)
  $$
  usually we ignore/drop minor terms.

**How to calc?**

```python
x = 5 + (12*34) # O(1)
y = 14-2        # O(1)
print x + y     # O(1)

# 3 * O(1) = O(1) because the constans get ignored.

# Linear Time
for x in range(0, n): # -|
	print x 		 #  O(n)

    # o(n) 
y = 5 + (13*34)		 # O(1)
for x in range(0, n): # O(n) -|
	print x			 #      -|
    
    # O(1) + O(N) = O(N)
    
    
# Quadratic Time
for x in range(0, n): # O(n) -|
	for x in range(0, n): # O(n) -|
		print x
    # O(n^2)
    
y = 5 + (13*34)		 		# O(1)
for x in range(0, n): 		# O(n)
	print x
for x in range(0, n): 		# |
	for x in range(0, n):   # | -> O(N^2)
		print x			   # | 
# In this case the BigO notations is equal to the bigger of all -> O(N^2)
```

In practice:

1. Constants matter
2. be aware of best-case and average-case