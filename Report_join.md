## Join and Sort-Merge Joint Tests

### Test1
 **Query:** Find the names of sailors who have reserved boat number 1.
       and print out the date of reservation.  

  SELECT S.sname, R.date  
  FROM   Sailors S, Reserves R  
  WHERE  S.sid = R.sid AND R.bid = 1  

Sailor file and Reserve file are sorted based on respective SID as key.  
Then sort-merge happens based on the inner join condition (S.sid=R.sid).  
Records are filtered based on BID(=1).  
The output consists of sailor names and reservation dates.  

### Test2
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

### Test3
**Query:** Find the names of sailors who have reserved a boat.  

  SELECT S.sname  
  FROM   Sailors S, Reserves R  
  WHERE  S.sid = R.sid  

Sailor file and Reserve file are sorted based on respective SID as key.  
Then sort-merge happens based on the inner join condition (S.sid=R.sid).  
The output consists of sailor names.  

### Test4
**Query:** Find the names of sailors who have reserved a boat
       and print each name once.  

  SELECT DISTINCT S.sname  
  FROM   Sailors S, Reserves R  
  WHERE  S.sid = R.sid  

Sailor file and Reserve file are sorted based on respective SID as key.  
Then sort-merge happens based on the implicit inner join condition (S.sid=R.sid).  
Then the duplicate records are removed using the DISTINCT keyword.  
The output consists of sailor names.  

### Test5
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

### Test6
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