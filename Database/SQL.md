## Key
- Super key
  - A set of one or more attributes (columns), which can uniquely identify a row in a table.
- Candidate key
  - They are also termed as *minimal super key*.
  - All the candidate keys are super keys.
  - Candidate keys are selected from the set of super keys
  - The only thing we take care while selecting candidate key is: It should not have any redundant attribute
- Primary Key
  - A primary is a **single column** value used to identify a database record uniquely.
  - A Primary key is selected from a set of candidate keys
  - It has following attributes
    - A primary key cannot be NULL
    - A primary key value must be unique
    - The primary key values should rarely be changed
    - The primary key must be given a value when a new record is inserted.
- Composite Key
  - A composite key is a primary key composed of multiple columns.
- Foreign Key
  - Foreign Key references the primary key of another Table! It helps connect your tables.
  - A foreign key can have a different name from its primary key
  - It ensures rows in one table have corresponding rows in another
  - Unlike the Primary key, they do not have to be unique. Most often they aren't
  - Foreign keys can be null even though primary keys can not 

## Big O