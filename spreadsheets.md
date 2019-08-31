Beyond Spreadsheets
===================

This is a quick dump of some ideas inspired by a
[discussion](https://twitter.com/joe_no_privacy/status/1166883865836679168)
on Twitter.

Spreadsheets Suck
-----------------

The following applies primarily to Excel and Google Sheets. However
the only other remotely decent option I'm aware of (Apple Numbers) is
only better at layout.

### The Good

* easy visual layout is very intuitive and allows rapid development
* once you understand the basics of cells and formulas you can just go
* when entering a formula you can just click on the cells you want to
  reference. no need to think about variables if you don't want to
  (though you should)
* quasi-functional reactive programming. changes propagate
  instantly. you don't have to write a bunch of logic to deal with
  state and propogation thereof, even when you have numerous complex
  streams
* tables (the Excel construct) and pivot tables make it possible to
  query and sort data visually without writing any code
* pivot tables offer data aggregation capabilites that go beyond
  what's possible using standard, straightforward SQL, yet are much
  easier to learn and use quickly
* charts can be used to aggregate and present data visually without
  manual design effort

### The Bad

* it's tough to build something that actually needs state. have to
  delve into macros or equivalent and do some gross saving and loading
  of data if you want this, which has to be hacked on top of the basic
  model
* not really designed to handle records, so you can't really build a
  true database
* constrained UI options. everything has to fit in a spreadsheet
  layout
* all the language features are totally unique, for better and for
  worse. the lookup and reference functions are a primitive,
  constrained toolkit for querying data that you don't really see
  elsewhere due to their limitations. (Google Sheets has actually
  introduced a QUERY function that introduces a SQL-style language for
  spreadsheet data)
* aside from Google's QUERY function, querying data is limited to what
  you can do with tables and pivot tables, lacks the richness of SQL

### The Ugly

* mixed business logic, presentation, and data in one big mess
* the reliance on cell references almost inevitably leads to a big
  tightly coupled ball of a spaghetti
* spreadsheet tools are utterly devoid of useful programming
  primitives or abstractions that could enable more modular and
  maintainable code
* formulas are difficult to reuse. custom formula functions must be
  defined in a scripting language like VB or JS using a totally
  different paradigm. referencing existing functions is nontrivial
* can't reuse spreadsheets except by copying
* no way to use common programmer productivity tools like VCS,
  testing, etc.
* closest you get to variables are named cells
* if you have more than one table on the page, adding a row to just
  one is a pain
* the default way of working focuses on direct references to cells by
  their position, which gets ugly fast

Antecedents
-----------

* Microsoft Excel/Google Sheets are mostly equivalent and have already
  been addressed above.
* Apple Numbers doesn't seem to have caught on as a major competitor
  but it has one really killer feature: unlike Excel and Sheets, which
  both couple presentation to the spreadsheet cell grid layout, it
  lets you manage tables and charts as discrete objects on a generic
  canvas. This is compelling from a user interface perspective and it
  bypasses the issue of dealing with multiple independent tables on
  one page. This could be especially useful for dealing with data
  models.
* HyperCard is intensely visual, very usable, and very beginner
  friendly, but HyperTalk is a painful "natural programming" language
  and HyperCard is dead. The only living successor (LiveCode) is trash.
* FileMaker Pro/MS Access are worth noting as tools that made desktop
  CRUD app development relatively simple. However, they both have
  crappy, non-functional programming models that are bad for both
  beginners and experts. Access is weirdly technical, simultaneously
  quasi-accessible and yet requiring SQL and VBA knowledge to do
  anything of value. FileMaker tries to simplify programming with a
  weird visual model that ends up being a massive pain to work with.
* Pyspread is a spreadsheet that leverages Python. Python happens to
  resemble the traditional spreadsheet formula syntax so it's a pretty
  reasonable choice, but its paradigm encourages impure imperative
  code that deviates from the model.
* Wakeling hacked Haskell into Excel as a proof of concept [1].

One issue with pretty much all of the above is that virtually none of
them are compatible with standard programmers' productivity tools:
version control is a no-go with specialized file formats, the tools
mostly expect you to use their built in editors rather than your
editor of choice, automated testing is all but impossible, trying to
create some kind of continuous integration or delivery process sounds
like a nightmare.

* IPython/Jupyter Notebook is an interactive programming environment
  for various languages that's like a superpowered REPL. It enables
  the visual representation of your code's output, including tables,
  graphs, etc.
* [ipysheet](https://towardsdatascience.com/interactive-spreadsheets-in-jupyter-32ab6ec0f4ff)
  is a project to introduce spreadsheet functionality into
  Jupyter. This actually looks very interesting and the illustrations
  look a lot like what I've had in mind, except it requires the user
  to write a lot of code and it doesn't really fit into the functional
  stream-oriented paradigm that makes spreadsheets so
  intuitive. However, the ability to embed widgets in cells and to
  render spreadsheets dynamically is a key feature in what I'm
  envisioning.

We Can Do Better
----------------

What makes spreadsheets compelling is their ease of use, enabled by
the simple visual metaphor of a paper spreadsheet and the intuitive
programming model. Any solution should try to preserve the easy
intuition as much as possible, but add the depth to make it actually
useful.

Like HyperCard but with a functional language (maybe a lisp?) and
spreadsheets as the main interface model.

Design Considerations
---------------------

* Should be accessible to experienced spreadsheet users who aren't
  comfortable with traditional programming while providing the depth
  to be useful for experienced programmers.
* Should be possible to use with the programmer's toolkit of choice.

Requirements
------------

* a mechanism for formula reuse
* formulas should be functions in a real language, not the limited
  expressions they are today
* the same language should be usable for both custom function
  definitions as well as for macro/scripting capability
* rather than just sheets, there should be support for multiple tables
  and other representations on the same user interface
* the language should be a close to pure functional as possible
  without expecting the user to learn something like Haskell

Technical Considerations
------------------------

* if you refresh the entire document every time it's updated, then you
  don't even need the underlying language to support reactive
  programming

Starting Points
---------------

Wakeling hacked Haskell into Excel using cell comments. Putting the
sheer horror of this aside, I think the idea of starting with an
existing spreadsheet tool (but not Excel) is appealing for a POC.

Even simpler, just parsing a CSV and eval'ing any cell that starts
with an equals sign could be an option. With a language like Lisp you
could rely on parens or other brackets instead if desired.

References
----------

[1] WAKELING, D. (2007). Spreadsheet functional programming. Journal of Functional Programming, 17(1), 131-143. doi:10.1017/S0956796806006186
