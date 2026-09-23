# Library Management System — PostgreSQL

## Running Queries

**pgAdmin 4:**
1. Open pgAdmin 4 and connect to your server
2. Right-click on **LibraryDB** → **Query Tool**
3. Paste your SQL and press **F5** or click the Run button

**psql (command line):**
```bash
psql -U postgres -d LibraryDB
```
Then paste your SQL directly into the terminal.

---

## Sprint 1: Project Setup

Open pgAdmin 4, connect to your server, and create a new database called **LibraryDB**. Right-click it and open the Query Tool to start writing queries.

### Create Authors Table

```sql
CREATE TABLE authors (
   id SERIAL PRIMARY KEY,
   name VARCHAR(255),
   nationality VARCHAR(255),
   birth_year INT,
   death_year INT
);
```

### Create Books Table

```sql
CREATE TABLE books (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author_id INT REFERENCES authors(id),
    genres TEXT[],
    published_year INT,
    available BOOLEAN DEFAULT TRUE
);
```

### Create Patrons Table

```sql
CREATE TABLE patrons (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    borrowed_books INT[]
);
```

---

## Sprint 2: Insert Data

### Insert Authors

```sql
INSERT INTO authors (id, name, nationality, birth_year, death_year) VALUES
(1, 'George Orwell', 'British', 1903, 1950),
(2, 'Harper Lee', 'American', 1926, 2016),
(3, 'F. Scott Fitzgerald', 'American', 1896, 1940),
(4, 'Aldous Huxley', 'British', 1894, 1963),
(5, 'J.D. Salinger', 'American', 1919, 2010),
(6, 'Herman Melville', 'American', 1819, 1891),
(7, 'Jane Austen', 'British', 1775, 1817),
(8, 'Leo Tolstoy', 'Russian', 1828, 1910),
(9, 'Fyodor Dostoevsky', 'Russian', 1821, 1881),
(10, 'J.R.R. Tolkien', 'British', 1892, 1973);
```

### Insert Books

```sql
INSERT INTO books (id, title, author_id, genres, published_year, available) VALUES
(1, '1984', 1, ARRAY['Dystopian', 'Political Fiction'], 1949, TRUE),
(2, 'To Kill a Mockingbird', 2, ARRAY['Southern Gothic', 'Bildungsroman'], 1960, TRUE),
(3, 'The Great Gatsby', 3, ARRAY['Tragedy'], 1925, TRUE),
(4, 'Brave New World', 4, ARRAY['Dystopian', 'Science Fiction'], 1932, TRUE),
(5, 'The Catcher in the Rye', 5, ARRAY['Realist Novel', 'Bildungsroman'], 1951, TRUE),
(6, 'Moby-Dick', 6, ARRAY['Adventure Fiction'], 1851, TRUE),
(7, 'Pride and Prejudice', 7, ARRAY['Romantic Novel'], 1813, TRUE),
(8, 'War and Peace', 8, ARRAY['Historical Novel'], 1869, TRUE),
(9, 'Crime and Punishment', 9, ARRAY['Philosophical Novel'], 1866, TRUE),
(10, 'The Hobbit', 10, ARRAY['Fantasy'], 1937, TRUE);
```

### Insert Patrons

```sql
INSERT INTO patrons (id, name, email, borrowed_books) VALUES
(1, 'Alice Johnson', 'alice@example.com', ARRAY[]::INT[]),
(2, 'Bob Smith', 'bob@example.com', ARRAY[1, 2]),
(3, 'Carol White', 'carol@example.com', ARRAY[]::INT[]),
(4, 'David Brown', 'david@example.com', ARRAY[3]),
(5, 'Eve Davis', 'eve@example.com', ARRAY[]::INT[]),
(6, 'Frank Moore', 'frank@example.com', ARRAY[4, 5]),
(7, 'Grace Miller', 'grace@example.com', ARRAY[]::INT[]),
(8, 'Hank Wilson', 'hank@example.com', ARRAY[6]),
(9, 'Ivy Taylor', 'ivy@example.com', ARRAY[]::INT[]),
(10, 'Jack Anderson', 'jack@example.com', ARRAY[7, 8]);
```

---

## Sprint 3: Read Operations

### Get All Books

```sql
SELECT * FROM books;
```

### Get a Book by Title

```sql
SELECT * FROM books
WHERE title = 'To Kill a Mockingbird';
```

### Get All Books by a Specific Author

```sql
SELECT * FROM books
WHERE author_id = 7;
```

### Get All Available Books

```sql
SELECT * FROM books
WHERE available = TRUE;
```

---

## Sprint 4: Update Operations

### Mark a Book as Borrowed

```sql
UPDATE books
SET available = FALSE
WHERE id = 4;
```

### Add a New Genre to an Existing Book

Using `array_append` to add to the array without replacing existing genres:

```sql
UPDATE books
SET genres = array_append(genres, 'Classics')
WHERE id = 10;
```

### Add a Borrowed Book to a Patron's Record

```sql
UPDATE patrons
SET borrowed_books = array_append(borrowed_books, 6)
WHERE id = 2;
```

---

## Sprint 5: Delete Operations

### Delete a Book by Title

```sql
DELETE FROM books
WHERE title = '1984';
```

### Delete an Author by ID

Because the `authors` table has a relationship with `books` via a foreign key, you cannot delete an author who still has books in the database. You must delete the author's books first, then delete the author.

**Step 1 — Delete the author's books:**
```sql
DELETE FROM books WHERE author_id = 3;
```

**Step 2 — Delete the author:**
```sql
DELETE FROM authors WHERE id = 3;
```

---

## Sprint 6: Advanced Queries

### Find Books Published After 1950

```sql
SELECT * FROM books
WHERE published_year > 1950;
```

### Find All American Authors

```sql
SELECT * FROM authors
WHERE nationality = 'American';
```

### Set All Books as Available

```sql
UPDATE books
SET available = TRUE;
```

### Find All Books That Are Available AND Published After 1950

```sql
SELECT * FROM books
WHERE available = TRUE AND published_year > 1950;
```

### Find Authors Whose Name Contains "George"

```sql
SELECT * FROM authors
WHERE name LIKE '%George%';
```

### Increment the Published Year 1869 by 1

```sql
UPDATE books
SET published_year = published_year + 1
WHERE published_year = 1869;
```
