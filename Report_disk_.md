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
  