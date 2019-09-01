A Post-Spreadsheet Fantasy
==========================

This is my attempt at capturing the key ideas and terminology of the
post-spreadsheet solution, which I'll call just the
**post-spreadsheet**.

Structure
---------

The post-spreadsheet is an interactive, semi-visual programming
environment centered around **blocks** laid out on one or more
**canvases** in a **project**. The project comprises a directory
structure wherein canvases are files. Other files may be included,
especially supporting scripts that can be imported by block code, but
also documentation, configuration, media assets, tests, dependency
declaration files, etc.

The model needs to enable efficient and intuitive management of data,
logic, and presentation.

* the main workspace is the **canvas**. the canvas is a page which
  allows content to be laid out using **blocks**. for simplicity of
  layout, the canvas may be sub-divided into rows and columns which
  can contain blocks and can be recursively split into more rows and
  columns, allowing the user to define the layout visually
* **blocks** are visual abstractions over data, code, and
  content, which I'll try to elucidate further

### Canvas

* can toggle between different modes with different levels of editing
  available
  * design: presentation edit mode. blocks and content can be added,
    removed, rearranged, and configured.
  * edit: data edit mode. fields are enabled. options to configure
    blocks and layout are hidden
  * view: everything is shown statically. all fields are disabled

### Blocks

Blocks are the heart of the entire system so it's imperative they be
well-defined. This section is going to be a bit exploratory as I try
to do that.

* able to be hidden and shown
* first-class - you can put a block in a variable, manipulate it, pass
  it around, etc.
* representable both in the UI and code, both representations are
  equal
* define presentation and enable code organization

#### Definition

* Name: A unique name (namespaced to the canvas) that can be used to
  reference its return value.
* Initialization values: The underlying configuration of the block,
  akin to a class constructor.
* Return value: The result of the block's underlying expression,
  assigned to its return value.
* Type: Determines the class that should be used to construct the
  block and evaluate it.

The block appears on the canvas as a box containing one or more UI
components. The block's content is determined by the value of an
expression. The exact visual representation of the block is determined
by the block's type.

Blocks have names. In code, these names correspond to the return value
of the block's function.

As a best practice, business logic and other customizations should
live in supporting files rather than in the block configuration. This
could be enforced by requiring blocks to reference a specific name in
the project workspace and nothing else, but I'm uncertain that this
would work as expected.

#### Block types

* Constant: Lets the user specify a constant to be used anywhere.
* Variable: Holds a value and displays a configurable widget allowing
  it to be updated.
* Data table: Corresponds to a table in the data store. You can do all
  the usual CRUD operations, as well as filter and sort. Editing the
  block allows modification of the underlying table schema. Fields are
  typed, validation is configurable, and custom handlers can be added
  to add extra behavior on various events such as saving a record.
* Data source: Corresponds to some external data source, but treated
  the same as a table. Depending on the source, there may be some
  transformation required to distill it into a tabular representation.
* Table view: Allows a custom view of a single table. Fields can be
  hidden, rearranged, and renamed, and filters, sorts, and
  aggregations (custom columns, total rows and total columns) can be
  added without affecting the base table.
* Expression: Evalutes an expression and, optionally, represents the
  result with a configurable UI element. Pick the element from a drop
  down and then apply any extra options. Can be a table, list, chart,
  etc. or none. Custom widgets can be defined by libraries if desired.
* Action: Consists of a set of UI elements which define handlers for
  various user actions. The handlers are allowed to modify the
  application state and data (see "Side effects").
* Data entry: Like an Access form, you can scroll through records one
  by one, edit, and add new ones. Widgets and layout can be
  configured. Optionally, custom handlers can be added for various
  operations.
* Content: A block that contains some raw HTML. A template language
  allows values to be embedded in the HTML.
* Container: A block that can be styled and contains nested blocks
  configured in the UI.
    * Loop: A container that iterates over the values of an iterable
      expression and exposes them to an inner block defintion,
      allowing dynamically nested blocks.
    * Conditional: A container that renders one of several blocks
      based on a set of conditional expressions.
* Template: Define a block with a set of parameters that can be used
  in its expressions. This isn't visible outside of design mode.
* Invocation: Invoke a template block and pass parameters to it.
* Meta: Allows blocks to be defined as code within the UI. Consists of
  a single expression that returns one or more blocks which are
  displayed in place of the meta block.

:bulb: Container and Meta blocks could probably be the same, but I
sort of wanted a distinction between them. A container just wraps
styling around some blocks that can still be edited visually, whereas
a meta block is meant to encapsulate dynamically-generated blocks that
can't be visually edited.

##### Special case blocks

* Code: A container for arbitrary code. Can be used to define helper
  functions, etc. that will be available anywhere in the canvas.
* Import: Imports an external library into the namespace of the
  canvas. This could also be done in a Code block, so it's a
  convenience thing.

Data
----

To facilitate use of multiple data sources, all referenced data is
pulled into the project as it's needed and queried therein. The query
features may lazily load external data to minimize the upfront hit of
potentially loading a huge amount of information at once.

If an external query language is needed (e.g. SQL), data sources may
be configured with a query so that they only select the desired set of
information.

Language
--------

The post-spreadsheet as a semi-visual programming language. All the
visual components (blocks, canvases) have a 1:1 correspondence with
underlying textual code, but their visual representation is (with one
exception) coequal with the textual representation. If you define a
block in the UI, it should produce code that is reasonable, readable,
and human editable. The reverse is also true: If you write some code
(again with one exception), it should be editable in the UI just as if
you'd created it there.

The only exception to this principle is the case where a visual
element is defined dynamically inside some other code without a visual
representation. In that case, it probably doesn't make sense to edit
blocks visually. However, Container, Loop, Conditional, and Template
blocks offer mechanisms for this that can be edited visually, so this
should only be a problem if someone is doing something particularly
obscure.

This means that, just as you're able to build spreadsheets while only
having to write formulas, you can conceivably build an application
where the only plaintext code is formulas.

### Choice of language

Since it needs to be at least partially representable in the UI, the
language should be straightforward to parse and to generate. It should
also be easy to define DSLs and relatively beginner friendly.

### Query language

As mentioned above in "Data", we'll want to define a DSL for querying
and manipulating data.

### Evaluation order

Since blocks may be laid out arbitrarily, it will be necessary to
parse out the entire program, build its dependency graph, and then
evaluate in the proper order. It's also necessary to detect
cycles. Toplogical sort FTW.

### Side effects

The language should be a completely pure functional language. The only
way to cause side effects should be by defining a function that
accepts some state and modifies it.

Most functions inside blocks are forced to be pure simply because they
have no mechanism to manipulate global state. The exception is action
handlers, which are passed a state object and can return a series of
imperative state changes that will be applied.

Security
--------

Given that this is going to accept real code and potentially be
web-based, securing against malicious code is important. Some
potential threats:

* server side (if any user code gets run there)
  * using local file access to access the filesystem of the host
  * denial of service
  * resource abuse (e.g. bitcoin mining or even by accident with
    really suboptimal code)
* client side
  * XSS/CSRF/etc. through any client side HTML and JS. this applies to
    user-defined code as well as data.
* secret management - users probably shouldn't expose their database
  passwords and whatever else to anyone using the tool

If all user code gets run on the client, that solves some of the
server side issues but not the issue of secret management. Running
user code in a containers or lightweight VM is probably the safest way
to sandbox on the server side.

Open questions
--------------

### Blocks and tables

One issue I'm grappling with is how exactly data table blocks relate
to the underlying tables.

I'm inclined to say that the block should be
the identity of the table, and thus that a change to the block is a
change to the table.

IOW, blocks should not be representations, they should _be_ the
underlying code object. If you have a data table block on your canvas
that corresponds to a local table in your project, then changes to the
block should be changes to the underlying table. Likewise, if you have
a data view block, changing the block changes the view. If you delete
a block, the code behind it is gone.
