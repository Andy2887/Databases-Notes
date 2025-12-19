# File Storage

### Files, Pages, Records

Record: a row

Relation: a table

Page: the basic unit of data for disk

![image](/Users/yuanliheng/Desktop/Database%20Notes/assets/files_records_pages.jpg)



# File Types

A "file type" in a database system refers to the specific method used to organize **pages** and **records** on a disk.

### Heap File

A **heap file is a file type with no particular ordering of pages or of the records on pages**.



## Linked List Implementation

In the linked list implementation, each data page contains **records**, a **free space tracker**, and **pointers** (byte offsets) to the next and previous page. There is one header page that acts as the start of the file and separates the data pages into full pages and free pages. When space is needed, empty pages are allocated and appended to the free pages portion of the list. When free data pages become full, they are moved from the free space portion to the front of the full pages portion of the linked list.

![image](/Users/yuanliheng/Desktop/Database%20Notes/assets/LinkedList.png)



## Page Directory Implementation

The Page Directory implementation differs from the Linked List implementation by only using a **linked list for header pages**. Each header page contains a pointer (byte offset) to the next header page, and its entries contain both a **pointer to a data page** and **the amount of free space left within that data page**. Since our header pages’ entries store pointers to each data page, the data pages themselves no longer need to store pointers to neighboring pages.

![image](/Users/yuanliheng/Desktop/Database%20Notes/assets/PageDirectory.png)



### Sorted Files

A **sorted file is a file type where pages are ordered and records within each page are sorted by key(s)**. These files are implemented using Page Directories and enforce an ordering upon data pages based on how records are sorted.



Heap Files vs Sorted Files

heap files: faster insertions, but searching requires full scan

Sorted Files: faster search, slower insertion



# Record Types

**Fixed Length Records** (FLR) and **Variable Length Records** (VLR). 

FLRs only contain fixed length fields (integer, boolean, date, etc.). 

Meanwhile, VLRs contain both fixed length and variable length fields (eg. varchar), resulting in each VLR of the same schema having a potentially different number of bytes.



# Page Formats

Pages with Fixed Length Records

Pages containing FLRs always use page headers to store the number of records currently on the page.  

![image](/Users/yuanliheng/Desktop/Database%20Notes/assets/FLRPage.png)



## Pages with Variable Length Records

Each page uses a **page footer** that maintains a **slot directory** tracking **slot count**, **a free space pointer**, and **entries**. The footer starts from the bottom of the page rather than the top so that the slot directory has room to grow when records are inserted.

![image](/Users/yuanliheng/Desktop/Database%20Notes/assets/VLRPage.png)


