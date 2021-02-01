## Buffer Management Tests
### Test1
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

### Test2
**Test:** In this test we are trying to pin more pages than they are buffer frames. We are initially pinning pages such that there are no more unpinned buffers available. On top of that we are adding one more page after this.  
**Behavior:** FAIL  
**Why:** The test fails because we are pinning more pages that the available buffer frames. The buffer frames are limited in number because they have to be assigned on the main memory.  

**Test:** In this test we are pinning the first page twice and then freeing this doubly pinned first page. There is a PinCount variable for every frame in the buffer which keeps track of the number of pins. The PinCount gets incremented if the frame is pinned and decremented if the frame is unpinned. Now the pin count variable gets incremented twice.  
**Behavior:** FAIL  
**Why:** We can not free a page that is not unpinned. To free a page its pin count should be zero but in our case the pincount is two. So the test will fail.  

**Test:** In this test we are trying a unpin a page which is not in the buffer pool.  
**Behavior:** FAIL  
**Why:** We can only unpin a page that is pinned to the buffer pool. We cannot unpin a page that is not found in the buffer pool. So the test will fail.

### Test3

**Test:** In this test we are creating some new pages and pinning them to the buffer. Then we dirty those pages by writing some values on to them. Then we began unpinning some of the pages and leaving the rest pinned.  
**Behavior:** PASS  
**Why:** Test3 should pass as it applies the same functions and internal logic of test1.  

**Test:** Now we are pinning the pages again and verifying that the values are correct by checking them.  
**Behavior:** PASS  
**Why:** Test3 should pass as it applies the same functions and internal logic of test1.  