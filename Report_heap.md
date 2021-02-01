## Heap File Tests:
### Test 1
**Test:** We are trying to create a new heap file with the name "file_1".  
**Behaviour:** PASS  
**Why:** Heapfile() function from Heapfile.java works correctly  

**Test:** We are checking if our newly created heap is not leaving pages pinned.  
**Behaviour**: PASS  
**Why:** Since we are creating the heap and not scanning anything there must be no pages pinned. So the condition ```SystemDefs.JavabaseBM.getNumUnpinnedBuffers() != SystemDefs.JavabaseBM.getNumBuffers()``` which prints the error fails because getNumUnpinnedBuffers() and getNumBuffers() are equal because there is no pinned page.  

**Test:** We are iterating through 100 entries in a for loop and inserting dummy records of fixed length 32 into our new heap.  
**Behaviour:** PASS  
**Why:** Heapfile.java works correctly  

**Test:** We are checking if our newly created dummy records are not leaving pages pinned.  
**Behaviour:** PASS  
**Why:** Since we are just inserting records into the heap and not scanning anything there must be no pages pinned. getNumUnpinnedBuffers() and getNumBuffers() are equal because there is no pinned page.  

**Test:** We are checking if we are able to scan on the heap file  
**Behaviour:** PASS  
**Why:** Scan.java works correctly  

**Test:** We are checking if the scan on heap file is pinning a page.  
**Behaviour:** PASS  
**Why:** Since we started scanning the the heap file its page should be pinned to the buffer. getNumUnpinnedBuffers() and getNumBuffers() are not equal because there is a pinned page.  

**Test:** We are checking if the length and values of the scanned dummy records are equal to the values inserted in a sequential order.  
**Behaviour:** PASS  
**Why:** If all the tests are passing up until this point then the inserted values and the scanned values must be equal.

**Test:** We are checking if after completing the scan the heap file has upinned its page from the buffer.  
**Behaviour:** PASS  
**Why:** Since scanning the the heap file is completed it upins its page. getNumUnpinnedBuffers() and getNumBuffers() are equal because there is no pinned page.  

**Test:** Finally we are checking if the number of items we scanned and the items that we have inserted are equal.  
**Behaviour:** PASS  
**Why:** If all the tests are passing up until this point then the number of records inserted is equal to the number of records scanned.  

### Test 2

**Test:** We are opening the same heap file as in test 1  
**Behaviour:** PASS  
**Why:** We are passing the same file name "file_1" from test1 in which we stored our initial heap to Heapfile(). Heapfile() function from Heapfile.java is working correctly.  

**Test:** We are first scanning the records then deleting half the records from file1. We are deleting the records whose index is odd. We are also making sure that our deletion is not leaving any pages pinned after the operation is completed by closing the scan  
**Behaviour:** PASS  
**Why:** deleteRecord() function from Heapfile.java is working correctly. Since we are making sure that scan is closed by using scan.closescan() there must be no pages pinned. So the condition ```SystemDefs.JavabaseBM.getNumUnpinnedBuffers() != SystemDefs.JavabaseBM.getNumBuffers()``` which prints the error fails because getNumUnpinnedBuffers() and getNumBuffers() are equal because there is no pinned page.  

**Test:** We are now scanning the remaining records whose index is even and making sure that they have the correct values in them. We are skipping the odd records by adding 2 to the counter variable  
**Behaviour:** PASS  
**Why:** If all the tests are passing up until this point then the data that is entered will be the data that is going to be read.  

### Test 3

**Test:** Opening the same heap file that was used in test 1 and test2 by passing "file_1" as an argument to the Heapfile() function.  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code is working correctly.  

**Test:** We are replacing the fval of every even record by scanning the record and then updating the record with a new tuple initialized with the new values. We are using the updateRecord() method from Heapfile.java. Remember that we are not updating the odd records because they were deleted in the previous test. We are also making sure that our update is not leaving any pages pinned after the operation is completed by closing the scan  
**Behaviour:** PASS  
**Why:** updateRecord() function defined in Heapfile.java source code is working correctly. Since we are making sure that scan is closed by using scan.closescan() there must be no pages pinned. So the condition ```SystemDefs.JavabaseBM.getNumUnpinnedBuffers() != SystemDefs.JavabaseBM.getNumBuffers()``` which causes the test to fail does not execute because getNumUnpinnedBuffers() and getNumBuffers() are equal because there is no pinned page.  

**Test:** We are checking if the values we updated really got updated to the new value. We are using a new method getRecord() from Heapfile.java to read the data. Remember that we are chekcing only for even values as the odd records were deleted in the previous test.  
**Behaviour:** PASS  
**Why:** If all the tests are passing up until this point then the data that is updated will be the data that is going to be read.  

### Test 4

**Test:** We are trying to update the record with a tuple whose size is one lesser than the previous record.  
**Behaviour:** FAIL  
**Why:** We cant change size of the record which was already inserted and initialized to a different size.  

**Test:** We are trying to update the record with a record whose size is one greater than the previous record.  
**Behaviour:** FAIL  
**Why:** We cant change size of the record which was already inserted and initialized to a different size.  

**Test:** We are trying to insert a new record whose size is MINIBASE_PAGESIZE + 4 in bytes. We get heap.SpaceNotAvailableException exception.  
**Behaviour:** FAIL  
**Why:** We cant insert records which have size greater than the page size which is 1024 bytes.  