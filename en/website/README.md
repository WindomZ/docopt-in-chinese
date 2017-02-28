# docopt

Command-line interface description language

**docopt** helps you:

* define the interface for your command-line app, and
* automatically generate a parser for it.

**docopt** is based on conventions that have been used for decades in 
help messages and man pages for describing a program's interface. 
An interface description in **docopt** is such a help message, but formalized. 
Here is an example:

```markdown
Naval Fate.

Usage:
  naval_fate ship new <name>...
  naval_fate ship <name> move <x> <y> [--speed=<kn>]
  naval_fate ship shoot <x> <y>
  naval_fate mine (set|remove) <x> <y> [--moored|--drifting]
  naval_fate -h | --help
  naval_fate --version

Options:
  -h --help     Show this screen.
  --version     Show version.
  --speed=<kn>  Speed in knots [default: 10].
  --moored      Moored (anchored) mine.
  --drifting    Drifting mine.
```

The example describes the interface of executable **naval_fate**, 
which can be invoked with different combinations of 
commands (**ship**, **new**, **move**, etc.), 
options (**-h**, **--help**, **--speed=\<kn\>**, etc.) and 
positional arguments (**\<name\>**, **\<x\>**, **\<y\>**).

The example uses brackets "**[ ]**", parens "**( )**", pipes "**|**" and ellipsis "**...**" to describe optional, 
required, mutually exclusive, and repeating elements. Together, 
these elements form valid usage patterns, each starting with program's name **naval_fate**.

