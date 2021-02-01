#  CSE 510: Database Management Systems Implementation
  
  
##  Project Phase 1 
  
  
##  Report by Venkata Satya Sai Dheeraj Akula
  
  
##  Group 6
  
**Venkata Satya Sai Dheeraj Akula**  
**Riya Jignesh Brahmbhatt**  
**Haard Mehta**  
**Rutva Krishnakant Patel**  
**Karthik Nishant Sekhar**  
**Nithya Vardhan Reddy Veerati**  
  
#  Abstract
  
  
The goal of the project phase 1 is to understand the internal working details of Minibase database management system by running tests on it. This report is a simplified explanation of many such tests that check the functionality of minibase. These tests are run on various components of the minibase like Buffer manager, Disk space manager, heap files, B+ tree indices, sort and merge join functions .  
  
#  Introduction
  
The source code for bare bone implementation of database management system called the minibase is provided in a tar file which had to be extracted on a linux machine. Then the make command had to be run to build the binaries from the source code. Then the make test command is used to run the tests. Many tests ran automatically without any interaction from the user. The B tree tests were interactive in the sense that it takes arguments from command line. All the results from the tests are captured by the typescript file which has been added to the report at the end. I am hereby going to explain these tests.
  
#  Description of the tests
  
  
##  Buffer Management Tests
  
###  Test1
  
**Test:** In this test we are allocating a bunch of pages to our database. Since the granularity of the hard disk is limited the secondary memory is blocked in the form of pages.  
**Behavior:** PASS  
**Why:** We are allocating a healthy amount of pages to our database so no error is returned.  
  
**Test:** In this test we are writing data on the pages. First we need to pin a page that is copy the page from secondary memory to primary memory so that the database can access it. This process of transferring memory is handled by the Buffer Manager. After we finished writing we are unpinning the page. We are also setting the dirty boolean of the page to ON which means that the page has been modified by the database and needs to get updated in the secondary memory.  
**Behavior:** PASS  
**Why:** We are pinning a healthy amount of pages so no error is returned.  
  
**Test:** In this test we are reading the values that are written on each page. We need to again pin a page because we need to access it. Now we check if the values that we are reading are equal to the values that were written in the previous step. In this way we can ensure consistency of our operations.After we finished reading we are unpinning the page. We are also setting the dirty boolean of the page to ON.  
**Behavior:** PASS  
**Why:** We are pinning the same number of pages and accessing them as in the previous step so we get no errors.  
  
**Test:** In the final step we are freeing the pages that got allocated. This step ensures that the pages in the buffer whose dirty boolean is set to ON is being written to the database.  
**Behavior:** PASS  
**Why:** freePage() function defined in the BufMgr.java source file works correctly so we get no errors.  
  
###  Test2
  
**Test:** In this test we are trying to pin more pages than they are buffer frames. We are initially pinning pages such that there are no more unpinned buffers available. On top of that we are adding one more page after this.  
**Behavior:** FAIL  
**Why:** The test fails because we are pinning more pages that the available buffer frames. The buffer frames are limited in number because they have to be assigned on the main memory.  
  
**Test:** In this test we are pinning the first page twice and then freeing this doubly pinned first page. There is a PinCount variable for every frame in the buffer which keeps track of the number of pins. The PinCount gets incremented if the frame is pinned and decremented if the frame is unpinned. Now the pin count variable gets incremented twice.  
**Behavior:** FAIL  
**Why:** We can not free a page that is not unpinned. To free a page its pin count should be zero but in our case the pincount is two. So the test will fail.  
  
**Test:** In this test we are trying a unpin a page which is not in the buffer pool.  
**Behavior:** FAIL  
**Why:** We can only unpin a page that is pinned to the buffer pool. We cannot unpin a page that is not found in the buffer pool. So the test will fail.
  
###  Test3
  
  
**Test:** In this test we are creating some new pages and pinning them to the buffer. Then we dirty those pages by writing some values on to them. Then we began unpinning some of the pages and leaving the rest pinned.  
**Behavior:** PASS  
**Why:** Test3 should pass as it applies the same functions and internal logic of test1.  
  
**Test:** Now we are pinning the pages again and verifying that the values are correct by checking them.  
**Behavior:** PASS  
**Why:** Test3 should pass as it applies the same functions and internal logic of test1.  
  
  
  
##  Disk Space Management Tests
  
###  Test1
  
We are allocating some pages and file entries.  
  
We are then allocating a run of pages. We are writing a string value on some of these pages. We are making sure we are assigning enough space so that we can fit in the string.  
  
We are then de allocating the remaining pages that we did not write on.  
  
**Behavior:** PASS  
**Why:** We are performing some basic operations that are implemented in DB.java source code.  
  
###  Test2
  
We are opening some of the files that are created in test1 and deleting them.  
  
We check if the files that are not deleted are still existing.  
  
We are going to read stuff from the files that are existing and check if the string values are equal to the values inserted in test1.  
  
**Behavior:** PASS  
**Why:** We are performing some basic operations that that are implemented in DB.java source code.  
  
###  Test3
  
We are trying to lookup a deleted file entry. This returns us a NULL page id.  
We are trying to delete a deleted file entry. This throws a ```FileEntryNotFoundException``` exception because the file was already deleted.  
  
We are trying to delete a nonexistent file entry. This again throws a ```FileEntryNotFoundException``` exception because the file does not exist.  
  
We are trying to lookup a nonexistent file entry with the name "blargle". This returns us a NULL page id because the file does not exist in our db.  
  
We are trying to add a file entry thats already there. This throws a ```DuplicateEntryException``` exception because the file already exists.  
  
We are trying to add a file entry whose name is longer than the maximum defined size for the name. This throws a ```FileNameTooLongException``` exception because the name exceeds the MAX_NAME size which is 50.  
  
We are trying to allocate run of pages that are too long. We are assigning 9000 pages which gives us a ```OutOfSpaceException``` exception.  
  
We are trying to allocate negative run of pages. Which gives us a ```InvalidRunSizeException``` exception.  
  
We are trying to de allocate negative run of pages. Which gives us a ```InvalidRunSizeException``` exception.  
  
**Behavior:** FAIL  
**Why:** We are performing invalid operations to check if the program is failing as expected.
  
###  Test4
  
**Test:**  When starting this test we are making sure that none of the pages are pinned. We are making this check by comparing the values of unpinned frames with the total number of frames.  
**Behavior:**  PASS  
**Why:** Since no pages are used or scanned the number of pinned pages remain zero.
  
**Test:** We are allocating pages after accounting for the data base overhead. Database overhead is the memory that database uses to store meta data. So we are assigning the remaining memory to pages. So that no more additional memory is left for new pages.  
**Behavior:** PASS  
**Why:** Since we are accounting for the database overhead all the remaining memory can be used for allocation of pages.  
  
**Test:** We are allocating one more page after the previous step.  
**Behavior:** FAIL  
**Why:** In the previous step we made sure that we are leaving no more space for extra pages. So when we are assigning one more page we get ```OutOfSpaceException``` exception.  
  
**Test:** We are de allocating some of the pages namely pages 3-9 and 33-40.  
**Behavior:** PASS  
**Why:** We are performing valid operations using functions defined in DB.java source file.  
  
**Test:** We are allocating some of the pages that got de allocated in the previous step namely the pages 33-40.  
**Behavior:** PASS  
**Why:** The pages which got freed can be allocated again.  
  
**Test:** We are de allocating continued run of pages namely pages 11-17 and 18-28.   
**Behavior:** PASS  
**Why:** We are performing valid operations using functions defined in DB.java source file.  
  
**Test:** We are re allocating the pages that got de allocated in the previous step namely pages 11-28.  
**Behavior:** PASS  
**Why:** We are performing valid operations using functions defined in DB.java source file.  
  
**Test:** We are deleting some left over file entries. Now a significant number of file entries are added so that the directory surpasses a page.  
**Behavior:** PASS  
**Why:** We are performing valid operations  
  
**Test:** We are de allocating the last two pages to test the boundary conditions of the space map.  
**Behavior:** PASS  
**Why:** The boundary conditions are valid because we are passing the correct page id by subtracting the database overhead from dbsize to the deallocate_page() function.  
  
  
  
##  Heap File Tests:
  
###  Test 1
  
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
  
###  Test 2
  
  
**Test:** We are opening the same heap file as in test 1  
**Behaviour:** PASS  
**Why:** We are passing the same file name "file_1" from test1 in which we stored our initial heap to Heapfile(). Heapfile() function from Heapfile.java is working correctly.  
  
**Test:** We are first scanning the records then deleting half the records from file1. We are deleting the records whose index is odd. We are also making sure that our deletion is not leaving any pages pinned after the operation is completed by closing the scan  
**Behaviour:** PASS  
**Why:** deleteRecord() function from Heapfile.java is working correctly. Since we are making sure that scan is closed by using scan.closescan() there must be no pages pinned. So the condition ```SystemDefs.JavabaseBM.getNumUnpinnedBuffers() != SystemDefs.JavabaseBM.getNumBuffers()``` which prints the error fails because getNumUnpinnedBuffers() and getNumBuffers() are equal because there is no pinned page.  
  
**Test:** We are now scanning the remaining records whose index is even and making sure that they have the correct values in them. We are skipping the odd records by adding 2 to the counter variable  
**Behaviour:** PASS  
**Why:** If all the tests are passing up until this point then the data that is entered will be the data that is going to be read.  
  
###  Test 3
  
  
**Test:** Opening the same heap file that was used in test 1 and test2 by passing "file_1" as an argument to the Heapfile() function.  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code is working correctly.  
  
**Test:** We are replacing the fval of every even record by scanning the record and then updating the record with a new tuple initialized with the new values. We are using the updateRecord() method from Heapfile.java. Remember that we are not updating the odd records because they were deleted in the previous test. We are also making sure that our update is not leaving any pages pinned after the operation is completed by closing the scan  
**Behaviour:** PASS  
**Why:** updateRecord() function defined in Heapfile.java source code is working correctly. Since we are making sure that scan is closed by using scan.closescan() there must be no pages pinned. So the condition ```SystemDefs.JavabaseBM.getNumUnpinnedBuffers() != SystemDefs.JavabaseBM.getNumBuffers()``` which causes the test to fail does not execute because getNumUnpinnedBuffers() and getNumBuffers() are equal because there is no pinned page.  
  
**Test:** We are checking if the values we updated really got updated to the new value. We are using a new method getRecord() from Heapfile.java to read the data. Remember that we are chekcing only for even values as the odd records were deleted in the previous test.  
**Behaviour:** PASS  
**Why:** If all the tests are passing up until this point then the data that is updated will be the data that is going to be read.  
  
###  Test 4
  
  
**Test:** We are trying to update the record with a tuple whose size is one lesser than the previous record.  
**Behaviour:** FAIL  
**Why:** We cant change size of the record which was already inserted and initialized to a different size.  
  
**Test:** We are trying to update the record with a record whose size is one greater than the previous record.  
**Behaviour:** FAIL  
**Why:** We cant change size of the record which was already inserted and initialized to a different size.  
  
**Test:** We are trying to insert a new record whose size is MINIBASE_PAGESIZE + 4 in bytes. We get heap.SpaceNotAvailableException exception.  
**Behaviour:** FAIL  
**Why:** We cant insert records which have size greater than the page size which is 1024 bytes.  
  
  
  
##  Index tests
  
###  Test1
  
  
**Test:** We are creating a BTree with records of names. Initially we are opening the heapfile "test1.in" to populate the tuples. We are creating the tuples from data2[] array which contains a list of names. Then we are initializing the BTree making the string attribute as the desired field that gets indexed.  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code and BTreeFile() function defined in BTreeFile.java source code works correctly.  
  
**Test:** Now we are copying the values from heapfile to the BTree that got initialized. We are using the insert() function defined in  BTreeFile.java source code. We are inserting both the string name and rid in the BTreeIndex file.  
**Behaviour:** PASS  
**Why:** Insertion is done by insert() function defined in BTreeFile.java source code which works correctly.  
  
**Test:** We are checking if the records that got inserted into BTreeIndex file are having the same number of records as the data2[] array from which we are populating the BTreeIndex file. We are also additionally checking if the values of strings are also equal to values that got inserted.  
**Behaviour:** PASS  
**Why:** If tests until this point works correctly the records that get inserted must be equal to the records that gets read.
  
###  Test2
  
  
**Test:** We are opening the BTree index file created in test1. We are using the same heap file from test1. So this heap file and BTree is already populated with values form test1  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code and BTreeFile() function defined in BTreeFile.java source code works correctly.  
  
**Test:**  We are making an index scan with the key value of "dsilva" on BTree to check if we can retrieve that value. To perform the index scan we are setting up a expression then passing it on to IndexScan() function defined in IndexScan.java.  
**Behaviour:** PASS  
**Why:** The scan return "dsilva" which is our key value. IndexScan() function defined in IndexScan.java works correctly.
  
**Test:** We are making a range scan from Key value of "dsilva" to Key value of "yuc" on BTree to check if we can retrieve those values in order. To perform the range scan we are setting up a expression then passing it on to IndexScan() function defined in IndexScan.java. We are also making sure that these strings are equal to the strings in the array from which the btree is constructed.  
**Behaviour:** PASS  
**Why:**  The scan return key values {"dsilva", "dwiyono", "edwards", "evgueni", "feldmann", "flechtne", "frankief", "ginther", "gray", "guangshu", "gunawan", "hai", "handi", "harimin", "haris", "he", "heitzman", "honghu", "huxtable", "ireland", "jhowe", "joon", "josephin", "joyce","jsong", "juei-wen", "karsono", "keeler", "ketola", "kinc", "kurniawa","leela", "lukas", "mak", "marc", "markert", "meltz", "meyers", "mirwais", "muerle", "muthiah", "neuman", "newell", "peter", "raghu", "randal", "rathgebe", "robert", "savoy", "schiesl", "schleis", "scottc", "seo", "shi", "shun-kit", "siddiqui", "soma", "sonthi", "sungk", "susanc", "tak", "thiodore", "ulloa", "vharvey", "waic","wan", "wawrzon", "wenchao", "wlau", "xbao", "xiaoming", "xin", "yi-chun", "yiching", "yuc"} which are our required values. IndexScan() function defined in IndexScan.java works correctly.  
  
###  Test3
  
  
**Test:** We are making a new heap file "test3.in" and adding tuples with string field, int field and float field. We are creating the BTree index on the integer field.  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code and BTreeFile() function defined in BTreeFile.java source code works correctly.  
  
**Test:** Now we are copying the values from heapfile to the BTree that got initialized. We are using the insert() function defined in  BTreeFile.java source code. We are inserting both the Integer value and rid in the BTreeIndex file.  
**Behaviour:** PASS  
**Why:** Insertion is done by insert() function defined in  BTreeFile.java source code which works correctly.  
  
**Test:** We are making a range scan from Key value of 100 to Key value of 900 on BTree to check if we can retrieve those values in order. To perform the range scan we are setting up a expression then passing it on to IndexScan() function defined in IndexScan.java. We are also making sure that these values do not cross the lower limit and the upper limit.  
**Behaviour:** PASS  
**Why:** The scan returned values from {100..900} in order which are our required values.IndexScan() function defined in IndexScan.java works correctly.  
  
  
  
  
  
  
##  Join and Sort-Merge Joint Tests
  
  
###  Test1
  
 **Query:** Find the names of sailors who have reserved boat number 1.
       and print out the date of reservation.  
  
  SELECT S.sname, R.date  
  FROM   Sailors S, Reserves R  
  WHERE  S.sid = R.sid AND R.bid = 1  
  
Sailor file and Reserve file are sorted based on respective SID as key.  
Then sort-merge happens based on the inner join condition (S.sid=R.sid).  
Records are filtered based on BID(=1).  
The output consists of sailor names and reservation dates.  
  
###  Test2
  
**Query:** Find the names of sailors who have reserved a red boat
       and return them in alphabetical order.  
  
  SELECT   S.sname  
  FROM     Sailors S, Boats B, Reserves R  
  WHERE    S.sid = R.sid AND R.bid = B.bid AND B.color = 'red'  
  ORDER BY S.sname  
  
The BTree index is created on the Sailors SID key.  
Then inner join is performed on Sailors and Reserves files based on join condition (S.sid=R.sid).  
Then a nested loop join is performed on the above-retrieved records and the Boat files based on the join condition(R.bid=B.bid).  
The resulting records are filtered based on the boat color(=Red).  
The filtered records are sorted alphabetically(using ORDER BY).
The output consists of sailor names.
  
###  Test3
  
**Query:** Find the names of sailors who have reserved a boat.  
  
  SELECT S.sname  
  FROM   Sailors S, Reserves R  
  WHERE  S.sid = R.sid  
  
Sailor file and Reserve file are sorted based on respective SID as key.  
Then sort-merge happens based on the inner join condition (S.sid=R.sid).  
The output consists of sailor names.  
  
###  Test4
  
**Query:** Find the names of sailors who have reserved a boat
       and print each name once.  
  
  SELECT DISTINCT S.sname  
  FROM   Sailors S, Reserves R  
  WHERE  S.sid = R.sid  
  
Sailor file and Reserve file are sorted based on respective SID as key.  
Then sort-merge happens based on the implicit inner join condition (S.sid=R.sid).  
Then the duplicate records are removed using the DISTINCT keyword.  
The output consists of sailor names.  
  
###  Test5
  
**Query:** Find the names of old sailors or sailors with a rating less
       than 7, who have reserved a boat, (perhaps to increase the
       amount they have to pay to make a reservation).  
  
  SELECT S.sname, S.rating, S.age  
  FROM   Sailors S, Reserves R  
  WHERE  S.sid = R.sid and (S.age > 40 || S.rating < 7)  
  
 Sailor file and Reserve file are sorted based on respective SID as key.  
 Then sort-merge happens based on the implicit inner join condition (S.sid=R.sid).  
Resulting records are filtered based on the age(>40) or ratings(<7).
The output consists of sailor names, sailor ratings and sailor age.  
  
###  Test6
  
**Query:** Find the names of sailors with a rating greater than 7
  who have reserved a red boat, and print them out in sorted order.  
  
  SELECT   S.sname  
  FROM     Sailors S, Boats B, Reserves R  
  WHERE    S.sid = R.sid AND S.rating > 7 AND R.bid = B.bid AND B.color = 'red'  
  ORDER BY S.name  
  
Inner join is performed on Sailors and Reserves files based on join condition (S.sid=R.sid).  
The resulting records are filtered based on sailors’ ratings (>7).  
Nested loop join is performed on the above-retrieved records and the Boat files based on the join condition(R.bid=B.bid).  
The resulting records are filtered based on the boat color(=Red).  
The output consists of sailor names sorted alphabetically.  
  
  
  
##  Sort Tests
  
###  Test1
  
  
**Test:** We are creating a heap file with the name "test1.in". We are then initializing some tuples with string values from data1[] array . Then we are copying these tuples into the heap. Then we sort the values of the heap in ascending order after performing a file scan on the heap file. The sorting is done on the string attribute. We check if the values are sorted correctly by comparing them to the contents of data2[] array. Note: data1[] array consists of unsorted names whereas data2[] array consists of ascending order sorted names in the data1[] array.  
**Behaviour:** PASS  
**Why:** Sort() function from the Sort.java source file works correctly. Pass int 0 as an additional argument to sort in Ascending order.  
  
###  Test2
  
**Test:** We are creating a heap file with the name "test2.in". We are then initializing some tuples with string values from data1[] array . Then we are copying these tuples into the heap. Then we sort the values of the heap in descending order after performing a file scan on the heap file. The sorting is done using the string attribute. We check if the values are sorted correctly by comparing them to the contents of data2[] array.This time we are going to reverse iterate on the data2[] array to get the values in descending order. Note: data1[] array consists of unsorted names whereas data2[] array consists of ascending order sorted names in the data1[] array.  
**Behaviour:** PASS  
**Why:** Sort() function from the Sort.java source file works correctly. Pass int 1 as an additional argument to sort in descending order.  
  
###  Test3
  
**Test:** We are creating a heap file with the name "test3.in". We are then initializing some tuples with string, int and float attributes. The int attribute is set to a random value. Then we are copying these tuples into the heap. Then we sort the values of the heap in ascending order after performing a file scan on the heap file. The sorting is done on the int attribute. We check if the values are sorted correctly by comparing them to the previous element. We also perform another sort on the FLOAT attribute which was also set to a random value. Now we perform a descending order sort after performing a file scan on the heap file. We check if the values are sorted correctly by comparing them to the previous element.  
**Behaviour:** PASS  
**Why:** Sort() function from the Sort.java source file works correctly.  
  
###  Test4
  
**Test:** We are creating a 2 new heap files with the names "test4-1.in" and "test4-2.in". We are performing test1 and test2 simultaneously on them.  
**Behaviour:** PASS  
**Why:** Test1 and Test2 are passing.  
  
  
#  Conclusions
  
The Tests ensured that all the moving parts of the project got compiled successfully.  
All these tests showcased several features of minibase.  
I was able to get familiar with the different components of minibase by performing these tests and reading the documentation from the javadoc folder.  
  
#  Bibliography
  
“The Minibase Home Page.” Wisc.edu, 2021, research.cs.wisc.edu/coral/minibase/minibase.html. Accessed 28 Jan. 2021.  
  
“Minibase Overview.” Wisc.edu, 2021, research.cs.wisc.edu/coral/minibase/intro/single_user.html. Accessed 29 Jan. 2021.  
  
Dhanushka Madushan. “How Database B-Tree Indexing Works.” Dzone.com, DZone, 22 Nov. 2019, dzone.com/articles/database-btree-indexing-in-sqlite. Accessed 29 Jan. 2021.  
  
Unknown. “DBMS Indexing in DBMS - Javatpoint.” Www.javatpoint.com, 2011, www.javatpoint.com/indexing-in-dbms. Accessed 29 Jan. 2021.  
  
Unknown. “BUFFER MANAGEMENT.” Blogspot.com, Feb. 2021, dbmsfortech.blogspot.com/2016/05/buffer-management.html. Accessed 30 Jan. 2021.  
  
‌
  
#  Appendix
  
  
##  typescript file
  
  
```
Script started on 2021-01-25 18:27:29-07:00 [TERM="xterm-256color" TTY="/dev/pts/0" COLUMNS="189" LINES="42"]
]0;machine7@machine7-VirtualBox: ~/Desktop/Database_Management_System_Implementation/minjava/javaminibase/src[01;32mmachine7@machine7-VirtualBox[00m:[01;34m~/Desktop/Database_Management_System_Implementation/minjava/javaminibase/src[00m<img src="https://latex.codecogs.com/gif.latex?make%20testcd%20tests;%20make%20bmtest%20dbtest;%20whoami;%20make%20hftest%20bttest%20indextest%20jointest%20sorttest%20sortmergemake[1]:%20Entering%20directory%20&#x27;&#x2F;home&#x2F;machine7&#x2F;Desktop&#x2F;Database_Management_System_Implementation&#x2F;minjava&#x2F;javaminibase&#x2F;src&#x2F;tests&#x27;&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20TestDriver.java%20BMTest.java&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.BMTestRunning%20Buffer%20Management%20tests....Replacer:%20Clock%20%20Test%201%20does%20a%20simple%20test%20of%20normal%20buffer%20manager%20operations:%20%20-%20Allocate%20a%20bunch%20of%20new%20pages%20%20-%20Write%20something%20on%20each%20one%20%20-%20Read%20that%20something%20back%20from%20each%20one%20%20%20(because%20we&#x27;re%20buffering,%20this%20is%20where%20most%20of%20the%20writes%20happen)%20%20-%20Free%20the%20pages%20again%20%20Test%201%20completed%20successfully.%20%20Test%202%20exercises%20some%20illegal%20buffer%20manager%20operations:%20%20-%20Try%20to%20pin%20more%20pages%20than%20there%20are%20frames***%20Pinning%20too%20many%20pages%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20free%20a%20doubly-pinned%20page***%20Freeing%20a%20pinned%20page%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20unpin%20a%20page%20not%20in%20the%20buffer%20pool***%20Unpinning%20a%20page%20not%20in%20the%20buffer%20pool%20%20--&gt;%20Failed%20as%20expected%20%20%20Test%202%20completed%20successfully.%20%20Test%203%20exercises%20some%20of%20the%20internals%20of%20the%20buffer%20manager%20%20-%20Allocate%20and%20dirty%20some%20new%20pages,%20one%20at%20a%20time,%20and%20leave%20some%20pinned%20%20-%20Read%20the%20pages%20%20Test%203%20completed%20successfully....Buffer%20Management%20tests%20completely%20successfully.&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20TestDriver.java%20DBTest.java&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.DBTestRunning%20Disk%20Space%20Management%20tests....Replacer:%20Clock%20%20Test%201%20creates%20a%20new%20database%20and%20does%20some%20tests%20of%20normal%20operations:%20%20-%20Add%20some%20file%20entries%20%20-%20Allocate%20a%20run%20of%20pages%20%20-%20Write%20something%20on%20some%20of%20them%20%20-%20Deallocate%20the%20rest%20of%20them%20%20Test%201%20completed%20successfully.%20%20Test%202%20opens%20the%20database%20created%20in%20test%201%20and%20does%20some%20further%20tests:%20%20-%20Delete%20some%20of%20the%20file%20entries%20%20-%20Look%20up%20file%20entries%20that%20should%20still%20be%20there%20%20-%20Read%20stuff%20back%20from%20pages%20we%20wrote%20in%20test%201%20%20Test%202%20completed%20successfully.%20%20Test%203%20tests%20for%20some%20error%20conditions:%20%20-%20Look%20up%20a%20deleted%20file%20entry****%20Looking%20up%20a%20deleted%20file%20entry%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20delete%20a%20deleted%20entry%20again****%20Delete%20a%20deleted%20file%20entry%20again%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20delete%20a%20nonexistent%20file%20entry****%20Deleting%20a%20nonexistent%20file%20entry%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Look%20up%20a%20nonexistent%20file%20entry****%20Looking%20up%20a%20nonexistent%20file%20entry%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20add%20a%20file%20entry%20that&#x27;s%20already%20there****%20Adding%20a%20duplicate%20file%20entry%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20add%20a%20file%20entry%20whose%20name%20is%20too%20long****%20Adding%20a%20file%20entry%20with%20too%20long%20a%20name%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20allocate%20a%20run%20of%20pages%20that&#x27;s%20too%20long%20****%20Allocating%20a%20run%20that&#x27;s%20too%20long%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20allocate%20a%20negative%20run%20of%20pages%20****%20Allocating%20a%20negative%20run%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20deallocate%20a%20negative%20run%20of%20pages%20****%20Deallocating%20a%20negative%20run%20%20--&gt;%20Failed%20as%20expected%20%20%20Test%203%20completed%20successfully.%20%20Test%204%20tests%20some%20boundary%20conditions.%20%20%20%20(These%20tests%20are%20very%20implementation-specific.)%20%20-%20Make%20sure%20no%20pages%20are%20pinned%20%20-%20Allocate%20all%20pages%20remaining%20after%20DB%20overhead%20is%20accounted%20for%20%20-%20Attempt%20to%20allocate%20one%20more%20page****%20Allocating%20one%20additional%20page%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Free%20some%20of%20the%20allocated%20pages%20%20-%20Allocate%20some%20of%20the%20just-freed%20pages%20%20-%20Free%20two%20continued%20run%20of%20the%20allocated%20pages%20%20-%20Allocate%20back%20number%20of%20pages%20equal%20to%20the%20just%20freed%20pages%20%20-%20Add%20enough%20file%20entries%20that%20the%20directory%20must%20surpass%20a%20page%20%20-%20Make%20sure%20that%20the%20directory%20has%20taken%20up%20an%20extra%20page:%20try%20to%20%20%20%20allocate%20more%20pages%20than%20should%20be%20available****%20Allocating%20more%20pages%20than%20are%20now%20available%20%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20At%20this%20point,%20all%20pages%20should%20be%20claimed.%20%20Try%20to%20allocateone%20more.****%20Allocating%20one%20more%20page%20than%20there%20is%20%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Free%20the%20last%20two%20pages:%20this%20tests%20a%20boundary%20condition%20in%20the%20space%20map.%20%20Test%204%20completed%20successfully....Disk%20Space%20Management%20tests%20completely%20successfully.make[1]:%20Leaving%20directory%20&#x27;&#x2F;home&#x2F;machine7&#x2F;Desktop&#x2F;Database_Management_System_Implementation&#x2F;minjava&#x2F;javaminibase&#x2F;src&#x2F;tests&#x27;machine7make[1]:%20Entering%20directory%20&#x27;&#x2F;home&#x2F;machine7&#x2F;Desktop&#x2F;Database_Management_System_Implementation&#x2F;minjava&#x2F;javaminibase&#x2F;src&#x2F;tests&#x27;&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20TestDriver.java%20HFTest.java&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.HFTestRunning%20Heap%20File%20tests....Replacer:%20Clock%20%20Test%201:%20Insert%20and%20scan%20fixed-size%20records%20%20-%20Create%20a%20heap%20file%20%20-%20Add%20100%20records%20to%20the%20file%20%20-%20Scan%20the%20records%20just%20inserted%20%20Test%201%20completed%20successfully.%20%20Test%202:%20Delete%20fixed-size%20records%20%20-%20Open%20the%20same%20heap%20file%20as%20test%201%20%20-%20Delete%20half%20the%20records%20%20-%20Scan%20the%20remaining%20records%20%20Test%202%20completed%20successfully.%20%20Test%203:%20Update%20fixed-size%20records%20%20-%20Open%20the%20same%20heap%20file%20as%20tests%201%20and%202%20%20-%20Change%20the%20records%20%20-%20Check%20that%20the%20updates%20are%20really%20there%20%20Test%203%20completed%20successfully.%20%20Test%204:%20Test%20some%20error%20conditions%20%20-%20Try%20to%20change%20the%20size%20of%20a%20record****%20Shortening%20a%20record%20%20--&gt;%20Failed%20as%20expected%20****%20Lengthening%20a%20record%20%20--&gt;%20Failed%20as%20expected%20%20%20-%20Try%20to%20insert%20a%20record%20that&#x27;s%20too%20long****%20Inserting%20a%20too-long%20record%20%20--&gt;%20Failed%20as%20expected%20%20%20Test%204%20completed%20successfully....Heap%20File%20tests%20completely%20successfully.&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20TestDriver.java%20BTTest.java&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.BTTestReplacer:%20ClockRunning%20%20tests....%20*****************%20The%20file%20name%20is:%20AAA0%20%20**********--------------------------%20MENU%20------------------[0]%20%20%20Naive%20delete%20(new%20file)[1]%20%20%20Full%20delete(Default)%20(new%20file)[2]%20%20%20Print%20the%20B+%20Tree%20Structure[3]%20%20%20Print%20All%20Leaf%20Pages[4]%20%20%20Choose%20a%20Page%20to%20Print%20%20%20%20%20%20%20%20%20%20%20---Integer%20Key%20(for%20choices%20[6]-[14])%20---[5]%20%20%20Insert%20a%20Record[6]%20%20%20Delete%20a%20Record[7]%20%20%20Test1%20(new%20file):%20insert%20n%20records%20in%20order[8]%20%20%20Test2%20(new%20file):%20insert%20n%20records%20in%20reverse%20order[9]%20%20%20Test3%20(new%20file):%20insert%20n%20records%20in%20random%20order[10]%20%20Test4%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20and%20delete%20m%20records%20randomly[11]%20%20Delete%20some%20records[12]%20%20Initialize%20a%20Scan[13]%20%20Scan%20the%20next%20Record[14]%20%20Delete%20the%20just-scanned%20record%20%20%20%20%20%20%20%20%20%20%20---String%20Key%20(for%20choice%20[15])%20---[15]%20%20Test5%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20%20%20%20%20and%20delete%20m%20records%20randomly.[16]%20%20Close%20the%20file[17]%20%20Open%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[18]%20%20Destroy%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[19]%20%20Quit!Hi,%20make%20your%20choice%20:2The%20Tree%20is%20Empty!!!--------------------------%20MENU%20------------------[0]%20%20%20Naive%20delete%20(new%20file)[1]%20%20%20Full%20delete(Default)%20(new%20file)[2]%20%20%20Print%20the%20B+%20Tree%20Structure[3]%20%20%20Print%20All%20Leaf%20Pages[4]%20%20%20Choose%20a%20Page%20to%20Print%20%20%20%20%20%20%20%20%20%20%20---Integer%20Key%20(for%20choices%20[6]-[14])%20---[5]%20%20%20Insert%20a%20Record[6]%20%20%20Delete%20a%20Record[7]%20%20%20Test1%20(new%20file):%20insert%20n%20records%20in%20order[8]%20%20%20Test2%20(new%20file):%20insert%20n%20records%20in%20reverse%20order[9]%20%20%20Test3%20(new%20file):%20insert%20n%20records%20in%20random%20order[10]%20%20Test4%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20and%20delete%20m%20records%20randomly[11]%20%20Delete%20some%20records[12]%20%20Initialize%20a%20Scan[13]%20%20Scan%20the%20next%20Record[14]%20%20Delete%20the%20just-scanned%20record%20%20%20%20%20%20%20%20%20%20%20---String%20Key%20(for%20choice%20[15])%20---[15]%20%20Test5%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20%20%20%20%20and%20delete%20m%20records%20randomly.[16]%20%20Close%20the%20file[17]%20%20Open%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[18]%20%20Destroy%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[19]%20%20Quit!Hi,%20make%20your%20choice%20:7Please%20input%20the%20number%20of%20keys%20to%20insert:%205%20*****************%20The%20file%20name%20is:%20AAA1%20%20**********--------------------------%20MENU%20------------------[0]%20%20%20Naive%20delete%20(new%20file)[1]%20%20%20Full%20delete(Default)%20(new%20file)[2]%20%20%20Print%20the%20B+%20Tree%20Structure[3]%20%20%20Print%20All%20Leaf%20Pages[4]%20%20%20Choose%20a%20Page%20to%20Print%20%20%20%20%20%20%20%20%20%20%20---Integer%20Key%20(for%20choices%20[6]-[14])%20---[5]%20%20%20Insert%20a%20Record[6]%20%20%20Delete%20a%20Record[7]%20%20%20Test1%20(new%20file):%20insert%20n%20records%20in%20order[8]%20%20%20Test2%20(new%20file):%20insert%20n%20records%20in%20reverse%20order[9]%20%20%20Test3%20(new%20file):%20insert%20n%20records%20in%20random%20order[10]%20%20Test4%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20and%20delete%20m%20records%20randomly[11]%20%20Delete%20some%20records[12]%20%20Initialize%20a%20Scan[13]%20%20Scan%20the%20next%20Record[14]%20%20Delete%20the%20just-scanned%20record%20%20%20%20%20%20%20%20%20%20%20---String%20Key%20(for%20choice%20[15])%20---[15]%20%20Test5%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20%20%20%20%20and%20delete%20m%20records%20randomly.[16]%20%20Close%20the%20file[17]%20%20Open%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[18]%20%20Destroy%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[19]%20%20Quit!Hi,%20make%20your%20choice%20:2---------------The%20B+%20Tree%20Structure---------------1%20%20%20%20%204---------------%20End%20-----------------------------------------%20MENU%20------------------[0]%20%20%20Naive%20delete%20(new%20file)[1]%20%20%20Full%20delete(Default)%20(new%20file)[2]%20%20%20Print%20the%20B+%20Tree%20Structure[3]%20%20%20Print%20All%20Leaf%20Pages[4]%20%20%20Choose%20a%20Page%20to%20Print%20%20%20%20%20%20%20%20%20%20%20---Integer%20Key%20(for%20choices%20[6]-[14])%20---[5]%20%20%20Insert%20a%20Record[6]%20%20%20Delete%20a%20Record[7]%20%20%20Test1%20(new%20file):%20insert%20n%20records%20in%20order[8]%20%20%20Test2%20(new%20file):%20insert%20n%20records%20in%20reverse%20order[9]%20%20%20Test3%20(new%20file):%20insert%20n%20records%20in%20random%20order[10]%20%20Test4%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20and%20delete%20m%20records%20randomly[11]%20%20Delete%20some%20records[12]%20%20Initialize%20a%20Scan[13]%20%20Scan%20the%20next%20Record[14]%20%20Delete%20the%20just-scanned%20record%20%20%20%20%20%20%20%20%20%20%20---String%20Key%20(for%20choice%20[15])%20---[15]%20%20Test5%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20%20%20%20%20and%20delete%20m%20records%20randomly.[16]%20%20Close%20the%20file[17]%20%20Open%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[18]%20%20Destroy%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[19]%20%20Quit!Hi,%20make%20your%20choice%20:3---------------The%20B+%20Tree%20Leaf%20Pages---------------**************To%20Print%20an%20Leaf%20Page%20********Current%20Page%20ID:%204Left%20Link%20%20%20%20%20%20:%20-1Right%20Link%20%20%20%20%20:%20-10%20(key,%20[pageNo,%20slotNo]):%20%20%20(0,%20%20[%200%200%20]%20)1%20(key,%20[pageNo,%20slotNo]):%20%20%20(1,%20%20[%201%201%20]%20)2%20(key,%20[pageNo,%20slotNo]):%20%20%20(2,%20%20[%202%202%20]%20)3%20(key,%20[pageNo,%20slotNo]):%20%20%20(3,%20%20[%203%203%20]%20)4%20(key,%20[pageNo,%20slotNo]):%20%20%20(4,%20%20[%204%204%20]%20)**************%20END%20********-------------%20All%20Leaf%20Pages%20Have%20Been%20Printed%20----------------------------------%20MENU%20------------------[0]%20%20%20Naive%20delete%20(new%20file)[1]%20%20%20Full%20delete(Default)%20(new%20file)[2]%20%20%20Print%20the%20B+%20Tree%20Structure[3]%20%20%20Print%20All%20Leaf%20Pages[4]%20%20%20Choose%20a%20Page%20to%20Print%20%20%20%20%20%20%20%20%20%20%20---Integer%20Key%20(for%20choices%20[6]-[14])%20---[5]%20%20%20Insert%20a%20Record[6]%20%20%20Delete%20a%20Record[7]%20%20%20Test1%20(new%20file):%20insert%20n%20records%20in%20order[8]%20%20%20Test2%20(new%20file):%20insert%20n%20records%20in%20reverse%20order[9]%20%20%20Test3%20(new%20file):%20insert%20n%20records%20in%20random%20order[10]%20%20Test4%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20and%20delete%20m%20records%20randomly[11]%20%20Delete%20some%20records[12]%20%20Initialize%20a%20Scan[13]%20%20Scan%20the%20next%20Record[14]%20%20Delete%20the%20just-scanned%20record%20%20%20%20%20%20%20%20%20%20%20---String%20Key%20(for%20choice%20[15])%20---[15]%20%20Test5%20(new%20file):%20insert%20n%20records%20in%20random%20order%20%20%20%20%20%20%20%20%20%20and%20delete%20m%20records%20randomly.[16]%20%20Close%20the%20file[17]%20%20Open%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[18]%20%20Destroy%20which%20file%20(input%20an%20integer%20for%20the%20file%20name):%20[19]%20%20Quit!Hi,%20make%20your%20choice%20:19...%20Finished%20.&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20TestDriver.java%20IndexTest.java&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.IndexTestRunning%20Index%20tests....Replacer:%20Clock------------------------%20TEST%201%20--------------------------BTreeIndex%20created%20successfully.BTreeIndex%20file%20created%20successfully.Test1%20--%20Index%20Scan%20OK-------------------%20TEST%201%20completed%20---------------------------------------------%20TEST%202%20--------------------------BTreeIndex%20opened%20successfully.Test2%20--%20Index%20Scan%20OK-------------------%20TEST%202%20completed%20---------------------------------------------%20TEST%203%20--------------------------BTreeIndex%20created%20successfully.BTreeIndex%20file%20created%20successfully.Test3%20--%20Index%20scan%20on%20int%20key%20OK-------------------%20TEST%203%20completed%20---------------------...Index%20tests%20completely%20successfully.Index%20tests%20completed%20successfully&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20TestDriver.java%20JoinTest.javaNote:%20Some%20input%20files%20use%20unchecked%20or%20unsafe%20operations.Note:%20Recompile%20with%20-Xlint:unchecked%20for%20details.&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.JoinTestReplacer:%20ClockAny%20resemblance%20of%20persons%20in%20this%20database%20to%20people%20living%20or%20deadis%20purely%20coincidental.%20The%20contents%20of%20this%20database%20do%20not%20reflectthe%20views%20of%20the%20University,%20the%20Computer%20%20Sciences%20Department%20or%20thedevelopers...**********************Query1%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20who%20have%20reserved%20boat%20number%201.%20%20%20%20%20%20%20and%20print%20out%20the%20date%20of%20reservation.%20%20SELECT%20S.sname,%20R.date%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid%20AND%20R.bid%20=%201(Tests%20FileScan,%20Projection,%20and%20Sort-Merge%20Join)[Mike%20Carey,%2005&#x2F;10&#x2F;95][David%20Dewitt,%2005&#x2F;11&#x2F;95][Jeff%20Naughton,%2005&#x2F;12&#x2F;95]Query1%20completed%20successfully!*******************Query1%20finished!!!***************************************Query2%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20who%20have%20reserved%20a%20red%20boat%20%20%20%20%20%20%20and%20return%20them%20in%20alphabetical%20order.%20%20SELECT%20%20%20S.sname%20%20FROM%20%20%20%20%20Sailors%20S,%20Boats%20B,%20Reserves%20R%20%20WHERE%20%20%20%20S.sid%20=%20R.sid%20AND%20R.bid%20=%20B.bid%20AND%20B.color%20=%20&#x27;red&#x27;%20%20ORDER%20BY%20S.snamePlan%20used:%20Sort%20(Pi(sname)%20(Sigma(B.color=&#x27;red&#x27;)%20%20|&gt;&lt;|%20%20Pi(sname,%20bid)%20(S%20%20|&gt;&lt;|%20%20R)))(Tests%20File%20scan,%20Index%20scan%20,Projection,%20%20index%20selection,%20sort%20and%20simple%20nested-loop%20join.)After%20Building%20btree%20index%20on%20sailors.sid.[David%20Dewitt][Mike%20Carey][Raghu%20Ramakrishnan][Yannis%20Ioannidis]Query2%20completed%20successfully!*******************Query2%20finished!!!***************************************Query3%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20who%20have%20reserved%20a%20boat.%20%20SELECT%20S.sname%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid(Tests%20FileScan,%20Projection,%20and%20SortMerge%20Join.)[Mike%20Carey][Mike%20Carey][Mike%20Carey][David%20Dewitt][David%20Dewitt][Jeff%20Naughton][Miron%20Livny][Yannis%20Ioannidis][Raghu%20Ramakrishnan][Raghu%20Ramakrishnan]Query3%20completed%20successfully!*******************Query3%20finished!!!***************************************Query4%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20who%20have%20reserved%20a%20boat%20%20%20%20%20%20%20and%20print%20each%20name%20once.%20%20SELECT%20DISTINCT%20S.sname%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid(Tests%20FileScan,%20Projection,%20Sort-Merge%20Join%20and%20Duplication%20elimination.)[David%20Dewitt][Jeff%20Naughton][Mike%20Carey][Miron%20Livny][Raghu%20Ramakrishnan][Yannis%20Ioannidis]Query4%20completed%20successfully!*******************Query4%20finished!!!***************************************Query5%20strating%20*********************Query:%20Find%20the%20names%20of%20old%20sailors%20or%20sailors%20with%20a%20rating%20less%20%20%20%20%20%20%20than%207,%20who%20have%20reserved%20a%20boat,%20(perhaps%20to%20increase%20the%20%20%20%20%20%20%20amount%20they%20have%20to%20pay%20to%20make%20a%20reservation).%20%20SELECT%20S.sname,%20S.rating,%20S.age%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid%20and%20(S.age%20&gt;%2040%20||%20S.rating%20&lt;%207)(Tests%20FileScan,%20Multiple%20Selection,%20Projection,%20and%20Sort-Merge%20Join.)[Mike%20Carey,%209,%2040.3][Mike%20Carey,%209,%2040.3][Mike%20Carey,%209,%2040.3][David%20Dewitt,%2010,%2047.2][David%20Dewitt,%2010,%2047.2][Jeff%20Naughton,%205,%2035.0][Yannis%20Ioannidis,%208,%2040.2]Query5%20completed%20successfully!*******************Query5%20finished!!!***************************************Query6%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20with%20a%20rating%20greater%20than%207%20%20who%20have%20reserved%20a%20red%20boat,%20and%20print%20them%20out%20in%20sorted%20order.%20%20SELECT%20%20%20S.sname%20%20FROM%20%20%20%20%20Sailors%20S,%20Boats%20B,%20Reserves%20R%20%20WHERE%20%20%20%20S.sid%20=%20R.sid%20AND%20S.rating%20&gt;%207%20AND%20R.bid%20=%20B.bid%20%20%20%20%20%20%20%20%20%20%20%20AND%20B.color%20=%20&#x27;red&#x27;%20%20ORDER%20BY%20S.namePlan%20used:%20Sort(Pi(sname)%20(Sigma(B.color=&#x27;red&#x27;)%20%20|&gt;&lt;|%20%20Pi(sname,%20bid)%20(Sigma(S.rating%20&gt;%207)%20%20|&gt;&lt;|%20%20R)))(Tests%20FileScan,%20Multiple%20Selection,%20Projection,sort%20and%20nested-loop%20join.)After%20nested%20loop%20join%20S.sid|&gt;&lt;|R.sid.After%20nested%20loop%20join%20R.bid|&gt;&lt;|B.bid%20AND%20B.color=red.After%20sorting%20the%20output%20tuples.[David%20Dewitt][Mike%20Carey][Raghu%20Ramakrishnan][Yannis%20Ioannidis]Query6%20completed%20successfully!*******************Query6%20finished!!!*****************Finished%20joins%20testingjoin%20tests%20completed%20successfully&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20TestDriver.java%20SortTest.java&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.SortTestRunning%20Sort%20tests....Replacer:%20Clock------------------------%20TEST%201%20--------------------------Test1%20--%20Sorting%20OK-------------------%20TEST%201%20completed%20---------------------------------------------%20TEST%202%20--------------------------Test2%20--%20Sorting%20OK-------------------%20TEST%202%20completed%20---------------------------------------------%20TEST%203%20--------------------------%20--%20Sorting%20in%20ascending%20order%20on%20the%20int%20field%20--%20Test3%20--%20Sorting%20of%20int%20field%20OK%20--%20Sorting%20in%20descending%20order%20on%20the%20float%20field%20--%20Test3%20--%20Sorting%20of%20float%20field%20OK-------------------%20TEST%203%20completed%20---------------------------------------------%20TEST%204%20--------------------------Test4%20--%20Sorting%20OK-------------------%20TEST%204%20completed%20---------------------...Sort%20tests%20completely%20successfully.Sorting%20tests%20completed%20successfully&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;javac%20-classpath%20.:..%20SM_JoinTest.java%20TestDriver.javaNote:%20SM_JoinTest.java%20uses%20unchecked%20or%20unsafe%20operations.Note:%20Recompile%20with%20-Xlint:unchecked%20for%20details.&#x2F;usr&#x2F;lib&#x2F;jvm&#x2F;default-java&#x2F;bin&#x2F;java%20%20-classpath%20.:..%20tests.SM_JoinTestReplacer:%20ClockAny%20resemblance%20of%20persons%20in%20this%20database%20to%20people%20living%20or%20deadis%20purely%20coincidental.%20The%20contents%20of%20this%20database%20do%20not%20reflectthe%20views%20of%20the%20University,%20the%20Computer%20%20Sciences%20Department%20or%20thedevelopers...**********************Query1%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20who%20have%20reserved%20boat%20number%201.%20%20%20%20%20%20%20and%20print%20out%20the%20date%20of%20reservation.%20%20SELECT%20S.sname,%20R.date%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid%20AND%20R.bid%20=%201(Tests%20FileScan,%20Projection,%20and%20Sort-Merge%20Join)[Mike%20Carey,%2005&#x2F;10&#x2F;95][David%20Dewitt,%2005&#x2F;11&#x2F;95][Jeff%20Naughton,%2005&#x2F;12&#x2F;95]Query1%20completed%20successfully!*******************Query1%20finished!!!***************************************Query3%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20who%20have%20reserved%20a%20boat.%20%20SELECT%20S.sname%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid(Tests%20FileScan,%20Projection,%20and%20SortMerge%20Join.)[Mike%20Carey][Mike%20Carey][Mike%20Carey][David%20Dewitt][David%20Dewitt][Jeff%20Naughton][Miron%20Livny][Yannis%20Ioannidis][Raghu%20Ramakrishnan][Raghu%20Ramakrishnan]Query3%20completed%20successfully!*******************Query3%20finished!!!***************************************Query4%20strating%20*********************Query:%20Find%20the%20names%20of%20sailors%20who%20have%20reserved%20a%20boat%20%20%20%20%20%20%20and%20print%20each%20name%20once.%20%20SELECT%20DISTINCT%20S.sname%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid(Tests%20FileScan,%20Projection,%20Sort-Merge%20Join%20and%20Duplication%20elimination.)[David%20Dewitt][Jeff%20Naughton][Mike%20Carey][Miron%20Livny][Raghu%20Ramakrishnan][Yannis%20Ioannidis]Query4%20completed%20successfully!*******************Query4%20finished!!!***************************************Query5%20strating%20*********************Query:%20Find%20the%20names%20of%20old%20sailors%20or%20sailors%20with%20a%20rating%20less%20%20%20%20%20%20%20than%207,%20who%20have%20reserved%20a%20boat,%20(perhaps%20to%20increase%20the%20%20%20%20%20%20%20amount%20they%20have%20to%20pay%20to%20make%20a%20reservation).%20%20SELECT%20S.sname,%20S.rating,%20S.age%20%20FROM%20%20%20Sailors%20S,%20Reserves%20R%20%20WHERE%20%20S.sid%20=%20R.sid%20and%20(S.age%20&gt;%2040%20||%20S.rating%20&lt;%207)(Tests%20FileScan,%20Multiple%20Selection,%20Projection,%20and%20Sort-Merge%20Join.)[Mike%20Carey,%209,%2040.3][Mike%20Carey,%209,%2040.3][Mike%20Carey,%209,%2040.3][David%20Dewitt,%2010,%2047.2][David%20Dewitt,%2010,%2047.2][Jeff%20Naughton,%205,%2035.0][Yannis%20Ioannidis,%208,%2040.2]Query5%20completed%20successfully!*******************Query5%20finished!!!*****************Finished%20joins%20testingjoin%20tests%20completed%20successfullymake[1]:%20Leaving%20directory%20&#x27;&#x2F;home&#x2F;machine7&#x2F;Desktop&#x2F;Database_Management_System_Implementation&#x2F;minjava&#x2F;javaminibase&#x2F;src&#x2F;tests&#x27;]0;machine7@machine7-VirtualBox:%20~&#x2F;Desktop&#x2F;Database_Management_System_Implementation&#x2F;minjava&#x2F;javaminibase&#x2F;src[01;32mmachine7@machine7-VirtualBox[00m:[01;34m~&#x2F;Desktop&#x2F;Database_Management_System_Implementation&#x2F;minjava&#x2F;javaminibase&#x2F;src[00m"/> exit
  
Script done on 2021-01-25 18:32:39-07:00 [COMMAND_EXIT_CODE="0"]
  
```
  