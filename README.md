# Active Record Domain Modeling Project

In this assignment, you'll build out your own project, using Active Record to enact database relationships, from scratch.

## Objectives

1. Build out a complex set of associations, including the belongs to/has many, and many-to-many relationship.
2. Use the code you wrote! Build a simple command line interface class so that a user can interact with your database.

## Guidelines

In this app, you'll be working with a library domain model. You will have the following models:

* Category (think fiction, non-fiction, biography, science, etc.). A category has a:
  * name
  * has many books
* Book. A book has a:
  * title
  * belongs to a category
  * belongs to an author
* Author. An author has a:
  * name
  * has many books
* User. A user has a:
  * name

The relationship between books and users in a little complex. A user can have many books and a book can belong to many users. At what point in time should a book be added to a user's collection of books? When a user checks that book out of the library. You will need a join table to implement this many-to-many relationship.

You also need to keep track of the books a user has previously checked out, vs. the books a user has currently checked out. So, each "checked out book" should know the its due date, should know whether or not it was returned and should therefore know if that book is overdue for that user.

## Instructions

### Database Design

Go ahead and design your database. What do you tables need to look like? What foriegn keys need to be included in which tables? Do you need a join table? What columns should it have?

Create and run your migrations.

### Model Design

Once you have migrated your database, define you models. Remember to inherit them from `ActiveRecord::Base`. Use the appropriate macros to finish building your your associations.

### Model Methods

Write a `User` instance method that allows a user to "check out" a book.

```ruby
the_doctor = User.create(name: "The Doctor")
book = Book.create(title: "Tardis Manual")

the_doctor.check_out_book(book, due_date: "September 1st, 2016")

the_doctor.books
# => [#<Book:0x007f8973912 @title="Tardis Manual">]

book.users
# => [#<User:8x007f867390fe38 @name="The Doctor">]
```

When a user check's out a book, it should create a new UserBook record (or Checkout record or whatever you want to call you join table/model). That new UserBook record should have a attribute (and therefore table column) of `returned?` which should default to `false`

So, after running the code above, I should be able to run the following code and see the following return values:

```ruby
UserBook.where(user: the_doctor, returned: false)
# => [#<User:0x007f867390fe38
        @user=#<User:0x007290867390fe38 @name="The Doctor">,
        @book=#<Book:7x007f8673999 @title="Tardis Manual">,
        @returned=false>
      ]
```


Write  `User` instance method that allows a user to "return" a book.

```ruby
the_doctor.return_book(book)
the_doctor.books
# => [#<Book:0x007f8973912 @title="Tardis Manual">]

UserBook.where(user: the_doctor, returned: false)
# => []

UserBook.where(user: the_doctor, returned: true)
# => [#<User:0x007f867390fe38
        @user=#<User:0x007290867390fe38 @name="The Doctor">,
        @book=#<Book:7x007f8673999 @title="Tardis Manual">,
        @returned=false>
      ]
```

### Play around with your code

Run the console task, `rake console`, and play around with the code you wrote to make sure your associations are working properly. Make and save an instance of category, book, author and use. Add the book to your category's collection of books, add the book to the author's collection of books. Have the user "check out" the book. Have the user "return" the book.

### Command Line Interface

If you have time, build out a very simple CLI that does the following:

* Greet the user
* Ask the user for their name so you can find that user in your database
* Show the user a list of categories and ask them to choose a category whose books they want to see
* Show the user all the books in the chosen category
* Allow the user to choose a book to check out
* Check out that book for them
* Prompt and allow the user to return the book

*This functionality will require you to see you database with a few dummy categories, books and users. Put this code in your `db/seeds.rb` file and run `rake db:seed`.
