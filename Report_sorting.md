## Sort Tests
### Test1

**Test:** We are creating a heap file with the name "test1.in". We are then initializing some tuples with string values from data1[] array . Then we are copying these tuples into the heap. Then we sort the values of the heap in ascending order after performing a file scan on the heap file. The sorting is done on the string attribute. We check if the values are sorted correctly by comparing them to the contents of data2[] array. Note: data1[] array consists of unsorted names whereas data2[] array consists of ascending order sorted names in the data1[] array.  
**Behaviour:** PASS  
**Why:** Sort() function from the Sort.java source file works correctly. Pass int 0 as an additional argument to sort in Ascending order.  

### Test2
**Test:** We are creating a heap file with the name "test2.in". We are then initializing some tuples with string values from data1[] array . Then we are copying these tuples into the heap. Then we sort the values of the heap in descending order after performing a file scan on the heap file. The sorting is done using the string attribute. We check if the values are sorted correctly by comparing them to the contents of data2[] array.This time we are going to reverse iterate on the data2[] array to get the values in descending order. Note: data1[] array consists of unsorted names whereas data2[] array consists of ascending order sorted names in the data1[] array.  
**Behaviour:** PASS  
**Why:** Sort() function from the Sort.java source file works correctly. Pass int 1 as an additional argument to sort in descending order.  

### Test3
**Test:** We are creating a heap file with the name "test3.in". We are then initializing some tuples with string, int and float attributes. The int attribute is set to a random value. Then we are copying these tuples into the heap. Then we sort the values of the heap in ascending order after performing a file scan on the heap file. The sorting is done on the int attribute. We check if the values are sorted correctly by comparing them to the previous element. We also perform another sort on the FLOAT attribute which was also set to a random value. Now we perform a descending order sort after performing a file scan on the heap file. We check if the values are sorted correctly by comparing them to the previous element.  
**Behaviour:** PASS  
**Why:** Sort() function from the Sort.java source file works correctly.  

### Test4
**Test:** We are creating a 2 new heap files with the names "test4-1.in" and "test4-2.in". We are performing test1 and test2 simultaneously on them.  
**Behaviour:** PASS  
**Why:** Test1 and Test2 are passing.  