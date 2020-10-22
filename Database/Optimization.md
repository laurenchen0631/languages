## Database Normalization
- Normalization is a database design technique that reduces data redundancy and eliminates undesirable characteristics.
- The Theory of Data Normalization in SQL is still being developed further. 
- In most practical applications, normalization achieves its best in 3rd Normal Form.

| Full Names  | Address                | Movies Rented                                  | Salutation |
|-------------|------------------------|------------------------------------------------|------------|
| Janet Jones | First Street Plot No 4 | Pirates of Caribbean, Clash of the Titans      | Ms.        |
| Robert Phil | 3rd Street 34          | Forgetting Sarah Marshal, Daddy's Little Girls | Mr.        |
| Robert Phil | 5th Avenue             | Clash of the Titans                            | Mr.        |

- 1NF (First Normal Form)
  - Each table cell should contain a single value.
  - Each record needs to be unique. (Attribute Domain should not change)
    - For example: If you have a column `dob` to save *date of births* of a set of people, then you cannot or you must not save 'names' of some of them in that column along with 'date of birth' of others in that column. It should hold only 'date of birth' for all the records/rows.

| Full Names  | Address                | Movies Rented            | Salutation |
|-------------|------------------------|--------------------------|------------|
| Janet Jones | First Street Plot No 4 | Pirates of Caribbean     | Ms.        |
| Janet Jones | First Street Plot No 4 | Clash of the Titans      | Ms.        |
| Robert Phil | 3rd Street 34          | Forgetting Sarah Marshal | Mr.        |
| Robert Phil | 3rd Street 34          | Daddy's Little Girls     | Mr.        |
| Robert Phil | 5th Avenue             | Clash of the Titans      | Mr.        |

- **2NF** (Second Normal Form)
  - Be in 1NF
  - There should be no Partial Dependency.
    - All non-key attributes are fully functional dependent on the primary key
    - If the primary key is not a composite key, all non-key attributes are always fully functional dependent on the primary key.
    - All non-key attributes cannot be dependent on a subset of the primary key. 
    - 2NF allows non-prime attributes to be functionally dependent on non-prime attributes

| Membership ID | Full Names  | Address                | Salutation |
|---------------|-------------|------------------------|------------|
| 1             | Janet Jones | First Street Plot No 4 | Ms.        |
| 2             | Robert Phil | 3rd Street 34          | Mr.        |
| 3             | Robert Phil | 5th Avenue             | Mr.        |

| Membership ID | Movies Rented            |
|---------------|--------------------------|
| 1             | Pirates of Caribbean     |
| 1             | Clash of the Titans      |
| 2             | Forgetting Sarah Marshal |
| 2             | Daddy's Little Girls     |
| 3             | Clash of the Titans      |

- **3NF** (Third Normal Form)
  - Be in 2NF
  - Has no *transitive functional dependencies*
    - A is functionally dependent on B, and B is functionally dependent on C. In this case, C is transitively dependent on A via B.
    - In other words, 3NF is violated when a non-prime attribute depends on other non-prime attributes rather than on the prime attributes or primary key.

  | Membership ID | Full Names  | Address                | Salutation ID |
  |---------------|-------------|------------------------|---------------|
  | 1             | Janet Jones | First Street Plot No 4 | 2             |
  | 2             | Robert Phil | 3rd Street 34          | 1             |
  | 3             | Robert Phil | 5th Avenue             | 1             |

  | Salutation ID | Salutation |
  |---------------|------------|
  | 1             | Mr.        |
  | 2             | Ms.        |
  | 3             | Mrs.       |
  | 4             | Dr.        |

  | Membership ID | Movies Rented            |
  |---------------|--------------------------|
  | 1             | Pirates of Caribbean     |
  | 1             | Clash of the Titans      |
  | 2             | Forgetting Sarah Marshal |
  | 2             | Daddy's Little Girls     |
  | 3             | Clash of the Titans      |

- BCNF (Boyce-Codd Normal Form)

## DAO 

## Reference
- [Normalization of Database](https://www.studytonight.com/dbms/database-normalization.php)
- [What is Normalization?](https://www.guru99.com/database-normalization.html)