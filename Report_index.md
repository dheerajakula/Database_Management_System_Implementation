## Index tests
### Test1

**Test:** We are creating a BTree with records of names. Initially we are opening the heapfile "test1.in" to populate the tuples. We are creating the tuples from data2[] array which contains a list of names. Then we are initializing the BTree making the string attribute as the desired field that gets indexed.  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code and BTreeFile() function defined in BTreeFile.java source code works correctly.  

**Test:** Now we are copying the values from heapfile to the BTree that got initialized. We are using the insert() function defined in  BTreeFile.java source code. We are inserting both the string name and rid in the BTreeIndex file.  
**Behaviour:** PASS  
**Why:** Insertion is done by insert() function defined in BTreeFile.java source code which works correctly.  

**Test:** We are checking if the records that got inserted into BTreeIndex file are having the same number of records as the data2[] array from which we are populating the BTreeIndex file. We are also additionally checking if the values of strings are also equal to values that got inserted.  
**Behaviour:** PASS  
**Why:** If tests until this point works correctly the records that get inserted must be equal to the records that gets read.

### Test2

**Test:** We are opening the BTree index file created in test1. We are using the same heap file from test1. So this heap file and BTree is already populated with values form test1  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code and BTreeFile() function defined in BTreeFile.java source code works correctly.  

**Test:**  We are making an index scan with the key value of "dsilva" on BTree to check if we can retrieve that value. To perform the index scan we are setting up a expression then passing it on to IndexScan() function defined in IndexScan.java.  
**Behaviour:** PASS  
**Why:** The scan return "dsilva" which is our key value. IndexScan() function defined in IndexScan.java works correctly.

**Test:** We are making a range scan from Key value of "dsilva" to Key value of "yuc" on BTree to check if we can retrieve those values in order. To perform the range scan we are setting up a expression then passing it on to IndexScan() function defined in IndexScan.java. We are also making sure that these strings are equal to the strings in the array from which the btree is constructed.  
**Behaviour:** PASS  
**Why:**  The scan return key values {"dsilva", "dwiyono", "edwards", "evgueni", "feldmann", "flechtne", "frankief", "ginther", "gray", "guangshu", "gunawan", "hai", "handi", "harimin", "haris", "he", "heitzman", "honghu", "huxtable", "ireland", "jhowe", "joon", "josephin", "joyce","jsong", "juei-wen", "karsono", "keeler", "ketola", "kinc", "kurniawa","leela", "lukas", "mak", "marc", "markert", "meltz", "meyers", "mirwais", "muerle", "muthiah", "neuman", "newell", "peter", "raghu", "randal", "rathgebe", "robert", "savoy", "schiesl", "schleis", "scottc", "seo", "shi", "shun-kit", "siddiqui", "soma", "sonthi", "sungk", "susanc", "tak", "thiodore", "ulloa", "vharvey", "waic","wan", "wawrzon", "wenchao", "wlau", "xbao", "xiaoming", "xin", "yi-chun", "yiching", "yuc"} which are our required values. IndexScan() function defined in IndexScan.java works correctly.  

### Test3

**Test:** We are making a new heap file "test3.in" and adding tuples with string field, int field and float field. We are creating the BTree index on the integer field.  
**Behaviour:** PASS  
**Why:** Heapfile() function defined in Heapfile.java source code and BTreeFile() function defined in BTreeFile.java source code works correctly.  

**Test:** Now we are copying the values from heapfile to the BTree that got initialized. We are using the insert() function defined in  BTreeFile.java source code. We are inserting both the Integer value and rid in the BTreeIndex file.  
**Behaviour:** PASS  
**Why:** Insertion is done by insert() function defined in  BTreeFile.java source code which works correctly.  

**Test:** We are making a range scan from Key value of 100 to Key value of 900 on BTree to check if we can retrieve those values in order. To perform the range scan we are setting up a expression then passing it on to IndexScan() function defined in IndexScan.java. We are also making sure that these values do not cross the lower limit and the upper limit.  
**Behaviour:** PASS  
**Why:** The scan returned values from {100..900} in order which are our required values.IndexScan() function defined in IndexScan.java works correctly.  