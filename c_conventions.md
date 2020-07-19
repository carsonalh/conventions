# My Coding Standard for C (in C99)

## File, Directory, and Build System Conventions

Put the external (interface) header in an "include" directory, and the rest of
the files, including the internal headers, in the source folder. Per module, it
should rarely be the case that sub-directories are necessary in the source
folder. If this is ever the case, and there are more than about 20 files (each
about 200-800 lines in size) in your library, you're probably trying to do too
much with the one library; make this more than one library.

Put everything one library will need in one header (expect this to be about
600-1000 lines of code, on average), and then only worry about splitting files
when it comes to implementation.

Files should be named in all lower-case, with underscores (_) separating each
word in the file name (otherwise known as snake-case).

Have no more than one `CMakeLists.txt` per library, as otherwise this will get
really messy. Inside each `CMakeLists.txt`, try to use as little user-defined
functions as possible, as duplicated code is better than ambigiuous and
hard-to-understand code.

## Identifier Naming Conventions

An important note on naming conventions specified by this standard:

> Every name in the library that is created should be as specific as is
> feasible; it is always better to have verbose names that well describe the
> purpose of a function/enum/union/struct than it is to have names that leave
> the reader and users of your code confused as to what it does: no amount of
> comments you put in *your* source will affect how usable it is to other
> people's code.

Some of the definitions that will be referenced throughout this standard are:

* `module_abbreviation`: A 2-to-4 letter (approximately) abbrevation of the
name of the library, which would typically be in lower case; it is more
important that the casing used throughout the code is consistent than anything
else. This can be longer, but it is convention to make this short, as it is the
one thing about your codebase you should expect the reader to memorise.

### Global Variable Naming Conventions

Don't use global variables. Full stop.

### Local Variable Naming Conventions

Use snake case for all local variables; you also shouldn't have to use letter
flags to denote the type information if it's a local variable.  I don't want to
see any local variables that have a \_ui suffix to denote `unsigned int`; their
scope shouldn't be large enough to warrant that much specificity, as they
shouldn't have to scroll up much to see the definition of the variable!

### Struct, Enum, and Union Naming Conventions

When naming a struct, enum, or union, it is important to make sure that
namespace pollution is mitigated to the best of our ability as programmers of
this library. It is therefore dictated by this standard that each struct name
should follow the following structure:

`<module_abbreviation>_<StructNameInCapitalCase>`

An example of this convention below, for a function in a module called
"flourish" (entirely fictional), that stores data about a file (i'm not sure
what to do for an example, ok).

```c
struct fl_FileData {
    /* Struct contents here... */
};

enum fl_FileState {
    /* Enum contents here... */
};
```

Ideally, a struct's name would be more descriptive than this, but you get the
point.

The name of members in a struct should all have the same rules for local
variables, as defined by this standard. An example is shown below.

```c
struct fl_FileData {
    const char *name;
    size_t size;
    uint32_t flag_descriptor;
    struct fl_Chunk chunks[];
};
```

### Function Naming Conventions

Every function in a module should follow the following pattern:

`<module_abbreviation>[_<StructName>]_<FunctionVerb>`

Where `FunctionVerb` is a detailed verb that describes the precise purpose of
that function, and each of the names shown in angle brackets are in snake case;
there should be no upper-case letters in *any* function names in the program.
It is important to note that with struct names, the casing will be different to
that of the struct definition, according to this standard.

If, however, there is no struct upon which the function is operating, then of
course, the programmer need not include a struct name, and the function can
instead be of the form `<module_abbreviation>_<FunctionVerb>`.

As another example, for our same fictional library "flourish", we may have a
function that saves data about our file to disk.

```c
void                fl_FileData_Save(struct fl_FileData *file_data);
enum fl_FileState   fl_FileData_GetState(const struct fl_FileData *file_data);
void                fl_InitializeApplication();
```

For internal functions, which are not intended to be called by the user of the
library, they should have the exact same rules as normal functions, with one
exception: they should begin with an underscore. An example of this is provided
below.

```c
void _fl_FileData_UpdateInternalState(struct fl_FileData *file_data);
```

### Enum Members, Macro Names, and Constant Variables

Enum members, as well as macro names, and constant variables, should all be
named in full upper-case separated by understores. The name should contain the
`<module_abbreviation>` in front of the name, again, to avoid namespace
pollution.

```c
#define FL_MIN_FILE_SIZE_BYTES 2048

enum fl_FileState {
    FL_FILE_STATE_OPEN,
    FL_FILE_STATE_CLOSE
};
```

## Other Miscellaneous Conventions

### Typedefs

**DO NOT USE TYPEDEFS WHERE NOT NECESSARY**. They make the code a lot more
ambiguous for not much reason; it's really not hard for you to type struct, and
when you do so, you **know** that you're using a struct, instead of being
really sure. This can stop the user of the library from making dumb mistakes
that would be preventable from not typedef'ing structs needlessly. This is not
to say that typedefs are bad, but in general, I would avoid them if you can.

### Header Guards

Make sure that every header file you ever write has a header guard; **PRAGMA
ONCE WILL NOT BE ACCEPTED**. The name of the header guard define should be the
name of the header file followed by a `_H` suffix.  For example, say the name
of our header file was `input_manager.h`. The name of the define in the header
guard should be `INPUT_MANAGER_H`.

### Switch Statements

Don't use them.

### VLAs (Variable Length Arrays)

Don't use them.

### Brace Formatting

For control flow, all of the braces in the application should follow the "java"
convention. This specifies that the opening brace to the control statement body
is on the same line as the control statement itself. Examples of this is
provided below.

```c
/* For loops: */
for (int i = 0; i < COUNT; i++) {
    /* Body */
}

/* While loops: */
while (condition) {
    /* Body */
}

/* If statements: */
if (condition) {
    /* Body */
}
```

Do not put anything on the same line after a closing brace. For example;

```c
/* This syntax */

if (condition) {
    /* Body */
}
else if (another_condition) {
    /* Body */
}
else {
    /* Body */
}

/* is better than this */

if (condition) {
    /* Body */
} else if (another_condition) {
    /* Body */
} else {
    /* Body */
}


/* This syntax */

do {
    /* Body */
}
while (condition);

/* is better than this */

do {
    /* Body */
} while (condition);
```

For function bodys, however, there is an exception for the brace placement.
Braces in function bodys should start on the line after the function signature.

```c
/* This syntax */

int main(void)
{
    /* Body */
}

/* is better than this */

int main(void) {
    /* Body */
}
```

### Indentation

Use 4 spaces per indent. This is always the case except for when splitting one
line into many other lines. In such a case, use 8 spaces for an indent (which
is the same as two normal indents, as per this standard).

```c
for (int i = 0; i < COUNT; i++) {
    if (fl_SatisfiesConstraint(i)) {
        /* Body */
    }
}

fl_SetupMetadata(
        &info,
        NUMBER_OF_CHUNKS,
        NULL
);
```

### Comments

Use only `/* */` style comments, and not `//` style comments.

