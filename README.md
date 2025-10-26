# bookstore
books
// insert_books.js
db.books.insertMany([
  {
    title: "The Great Gatsby",
    author: "F. Scott Fitzgerald",
    genre: "Classic",
    published_year: 1925,
    price: 12.99,
    in_stock: true,
    pages: 218,
    publisher: "Scribner"
  },
  {
    title: "To Kill a Mockingbird",
    author: "Harper Lee",
    genre: "Fiction",
    published_year: 1960,
    price: 14.99,
    in_stock: true,
    pages: 281,
    publisher: "J.B. Lippincott & Co."
  },
  {
    title: "1984",
    author: "George Orwell",
    genre: "Dystopian",
    published_year: 1949,
    price: 10.99,
    in_stock: false,
    pages: 328,
    publisher: "Secker & Warburg"
  },
  {
    title: "Pride and Prejudice",
    author: "Jane Austen",
    genre: "Romance",
    published_year: 1813,
    price: 9.99,
    in_stock: true,
    pages: 432,
    publisher: "T. Egerton"
  },
  {
    title: "The Hobbit",
    author: "J.R.R. Tolkien",
    genre: "Fantasy",
    published_year: 1937,
    price: 15.99,
    in_stock: true,
    pages: 310,
    publisher: "George Allen & Unwin"
  },
  {
    title: "Harry Potter and the Sorcerer's Stone",
    author: "J.K. Rowling",
    genre: "Fantasy",
    published_year: 1997,
    price: 18.99,
    in_stock: true,
    pages: 320,
    publisher: "Bloomsbury"
  },
  {
    title: "The Catcher in the Rye",
    author: "J.D. Salinger",
    genre: "Fiction",
    published_year: 1951,
    price: 11.99,
    in_stock: false,
    pages: 234,
    publisher: "Little, Brown and Company"
  },
  {
    title: "The Lord of the Rings",
    author: "J.R.R. Tolkien",
    genre: "Fantasy",
    published_year: 1954,
    price: 24.99,
    in_stock: true,
    pages: 1178,
    publisher: "George Allen & Unwin"
  },
  {
    title: "The Da Vinci Code",
    author: "Dan Brown",
    genre: "Mystery",
    published_year: 2003,
    price: 13.99,
    in_stock: true,
    pages: 489,
    publisher: "Doubleday"
  },
  {
    title: "The Alchemist",
    author: "Paulo Coelho",
    genre: "Fiction",
    published_year: 1988,
    price: 12.49,
    in_stock: true,
    pages: 208,
    publisher: "HarperTorch"
  },
  {
    title: "The Hunger Games",
    author: "Suzanne Collins",
    genre: "Dystopian",
    published_year: 2008,
    price: 16.99,
    in_stock: true,
    pages: 374,
    publisher: "Scholastic"
  },
  {
    title: "The Shining",
    author: "Stephen King",
    genre: "Horror",
    published_year: 1977,
    price: 14.49,
    in_stock: false,
    pages: 447,
    publisher: "Doubleday"
  }
]


// queries.js
// 1. Find all books in a specific genre
db.books.find({ genre: "Fantasy" })

// 2. Find books published after a certain year
db.books.find({ published_year: { $gt: 2000 } })

// 3. Find books by a specific author
db.books.find({ author: "J.R.R. Tolkien" })

// 4. Update the price of a specific book
db.books.updateOne(
  { title: "The Great Gatsby" },
  { $set: { price: 13.99 } }
)

// 5. Delete a book by its title
db.books.deleteOne({ title: "The Shining" })

// Advanced Queries

// 1. Find books that are both in stock and published after 2010
db.books.find({ 
  in_stock: true, 
  published_year: { $gt: 2010 } 
})

// 2. Use projection to return only title, author, and price
db.books.find(
  { genre: "Fantasy" },
  { title: 1, author: 1, price: 1, _id: 0 }
)

// 3. Implement sorting by price (ascending and descending)
// Ascending
db.books.find().sort({ price: 1 })

// Descending
db.books.find().sort({ price: -1 })

// 4. Implement pagination (5 books per page)
// Page 1
db.books.find().limit(5).skip(0)

// Page 2
db.books.find().limit(5).skip(5)

// Page 3
db.books.find().limit(5).skip(10)


// Aggregation Pipelines

// 1. Calculate average price of books by genre
db.books.aggregate([
  {
    $group: {
      _id: "$genre",
      averagePrice: { $avg: "$price" },
      bookCount: { $sum: 1 }
    }
  },
  {
    $sort: { averagePrice: -1 }
  }
])

// 2. Find author with the most books
db.books.aggregate([
  {
    $group: {
      _id: "$author",
      bookCount: { $sum: 1 }
    }
  },
  {
    $sort: { bookCount: -1 }
  },
  {
    $limit: 1
  }
])

// 3. Group books by publication decade and count them
db.books.aggregate([
  {
    $project: {
      title: 1,
      published_year: 1,
      decade: {
        $subtract: [
          "$published_year",
          { $mod: ["$published_year", 10] }
        ]
      }
    }
  },
  {
    $group: {
      _id: "$decade",
      bookCount: { $sum: 1 },
      books: { $push: "$title" }
    }
  },
  {
    $sort: { _id: 1 }
  }
])

// Indexing

// 1. Create index on title field
db.books.createIndex({ title: 1 })

// 2. Create compound index on author and published_year
db.books.createIndex({ author: 1, published_year: -1 })

// 3. Demonstrate performance improvement with explain()
// Without index
db.books.find({ title: "The Hobbit" }).explain("executionStats")

// With index (run after creating the index)
db.books.find({ title: "The Hobbit" }).explain("executionStats")

// Compare compound index vs regular query
db.books.find({ 
  author: "J.R.R. Tolkien", 
  published_year: { $gt: 1950 } 
}).explain("executionStats")
// Verify data insertion
db.books.countDocuments()

// Check indexes
db.books.getIndexes()

// Test a few queries to ensure everything works
db.books.find({ genre: "Fantasy" }).pretty()

// End of queries.js









