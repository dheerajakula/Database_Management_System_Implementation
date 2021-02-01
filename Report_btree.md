## B Tree Tests
B-tree is a self-balancing tree data structure that maintains sorted data and allows searches, sequential access, insertions, and deletions in logarithmic time.  

The B+ tree is a B tree that stores the data only at the leaf nodes of the tree. The leaf nodes of the B+ tree also allows sequential access of data because they are joined by pointers in the form of a linked list.  

These B Tree tests are interactive and requires the user to input an option ranging from 0-19.  

**Test 1:** 
In this test, there is a prompt to the user to enter the number of records (N). On entering this number, a new file is created to store the BTree file. Records ```(KEY, RID (pageno, i))``` are created in a for loop which run N times.

**Test 2:** 
In this test, there is a prompt to the user to enter the number of records (N). On entering this number, a new file is created to store the BTree file. Records ```(KEY, RID (pageno, N-i))``` are created in a for loop which run N times. The N records are inserted in reverse order.

**Test 3:** Insert N records at random. The records ```(KEY, RID (pageno, k))``` are created in the BTree file in for loop which runs N times. Where the value of k is randomly generated.  

**Test 4:** Insert N records then delete M records from the BTree. First M number of ```(KEY, RID)``` random value pairs are created and are checked if they are present in the BTree file. Delete the record if the b tree consists that pair. 