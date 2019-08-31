A Post-Spreadsheet Fantasy
==========================

This is my attempt at capturing the key ideas and terminology of the
post-spreadsheet solution, which I'll call just the
**post-spreadsheet**.

Overview
--------

The post-spreadsheet is an interactive programming environment
centered around **blocks** laid out on one or more **canvases** in a
**sketchbook**. The sketchbook comprises a directory structure wherein
canvases are files. Other files may be included, especially supporting
scripts that can be imported by block code, but also documentation,
configuration, media assets, tests, dependency declaration files, etc.

Key terms:

* block
  * widget
  * data
  * code
* canvas
  * blocks
  * elements - static items such as images or text that should not be
    dynamic
* sketchbook

As a best practice, blocks and canvases should contain a minimum of
code and data. This could be enforced by requiring data blocks to
reference a specific name in the sketchbook workspace and nothing
else, but I'm uncertain that this would work as expected.

User stories
------------

Let's consider some potential users. Their stories are aspirational,
but they should help envision the solution.

### Robert

Robert is a non-technical but experienced computer user who uses a
simple spreadsheet to help budget his expenses. He creates a
sketchbook and starts to replicate his budget spreadsheet.

He first creates a data table block for his transactions. The table
has columns for the Date, Account, Payee, Category, Amount, and a
Memo. He wants every field to be validated and formatted
appropriately, so Date should validate it's in a correct format of his
choosing and Amount should be represented as a currency value like
"$25.00". Account, Payee, and Category are dropdowns which should list
existing values and narrow down the options as he types. Creating a
new value should include some kind of clear feedback in case he makes
a typo and it should be easy to delete them. He shouldn't need to
worry about creating a separate table to contain these values. Since
he enters transactions quickly in batches, he should be able to enter
everything without taking his hands off the keyboard.

Once he has a reasonable subset of transactions, he wants to start
sorting and summarizing the data. First, he wants to see the net total
of every transaction to see how much money he has now. He creates a
new block and enters an expression to sum the values of the Amount
column in his Transactions table.

Next, he wants to filter his transactions table by each field for
quick exploration. He wants to be able to enter a string to search for
in the table. He wants to be able to select a subset of values for
each column and only display those, for example only showing the Payee
for his employer. He wants to be able to query by column, for example
only showing transactions from August 1 to August 15 of 2018, only
showing transactions where he spent over $100, or only showing
transactions where a particular keyword is in the memo field. In every
case, the net total should immediately update to only sum the
displayed items.

Now that he has this straightforward entry view, he wants to start
creating premade summary views for his use.

* table of spending by category
* pie chart of spending by payee
* pie chart of his cash assets by account
* pie chart of his debt balances by account
* bar chart of his net worth over time

Robert has several recurring bills that he pays every month on
different days. He wants to create a set of buttons that will
automatically add transactions for his rent, his phone bill, and his
Patreon payment. When clicked, the buttons should add a payment for a
fixed amount on the current date.

Since he knows one of the selling points of post-spreadsheets is
reusable code, Robert creates a script file and implements a function
that, given a transaction record, will automatically set the date to
today and save it. Then, he creates three button blocks that construct
each transaction and call the function to create the record.

Realizing that this could be prone to error, he decides he wants a
visual indicator of whether each transaction is paid. He needs a way
to construct a query that will check whether a transaction matching
the right payee has already been created in the past month. He creates
a data table block for each transaction that references his
transactions table as its data source and queries to only show the
appropriate payment for the month. He then updates each button block
to only be enabled if the data table for its corresponding transaction
does not contain any rows. He sets each button to have red text if
there is no transaction and green if there is one. He hides the
intermediate data tables, as the buttons' enabled or disabled status
is all he needs to see for his purposes.

### Grace

Grace is a UX designer who needs a tool for rapid prototyping.

* UI layout

### Sonji

Sonji is an expert programmer who hates boilerplate and wants a way to
quickly build tools for herself without getting bogged down. She will
only be convinced to use post-spreadsheets if she can implement a
sufficiently complex app in record time.

* code reuse
