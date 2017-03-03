``docopt`` creates *beautiful* command-line interfaces
======================================================================

.. image:: https://travis-ci.org/docopt/docopt.svg?branch=master
    :target: https://travis-ci.org/docopt/docopt

.. image:: https://img.shields.io/pypi/v/docopt.svg
    :target: https://pypi.python.org/pypi/docopt

Video introduction to **docopt**: `PyCon UK 2012: Create *beautiful*
command-line interfaces with Python <http://youtu.be/pXhcPJK5cMc>`_

    New in version 0.6.1:

    - Fix issue `#85 <https://github.com/docopt/docopt/issues/85>`_
      which caused improper handling of ``[options]`` shortcut
      if it was present several times.

    New in version 0.6.0:

    - New argument ``options_first``, disallows interspersing options
      and arguments.  If you supply ``options_first=True`` to
      ``docopt``, it will interpret all arguments as positional
      arguments after first positional argument.

    - If option with argument could be repeated, its default value
      will be interpreted as space-separated list. E.g. with
      ``[default: ./here ./there]`` will be interpreted as
      ``['./here', './there']``.

    Breaking changes:

    - Meaning of ``[options]`` shortcut slightly changed. Previously
      it meant *"any known option"*. Now it means *"any option not in
      usage-pattern"*.  This avoids the situation when an option is
      allowed to be repeated unintentionally.

    - ``argv`` is ``None`` by default, not ``sys.argv[1:]``.
      This allows ``docopt`` to always use the *latest* ``sys.argv``,
      not ``sys.argv`` during import time.

Isn't it awesome how ``optparse`` and ``argparse`` generate help
messages based on your code?!

*Hell no!*  You know what's awesome?  It's when the option parser *is*
generated based on the beautiful help message that you write yourself!
This way you don't need to write this stupid repeatable parser-code,
and instead can write only the help message--*the way you want it*.

**docopt** helps you create most beautiful command-line interfaces
*easily*:

.. code:: python

    """Naval Fate.

    Usage:
      naval_fate.py ship new <name>...
      naval_fate.py ship <name> move <x> <y> [--speed=<kn>]
      naval_fate.py ship shoot <x> <y>
      naval_fate.py mine (set|remove) <x> <y> [--moored | --drifting]
      naval_fate.py (-h | --help)
      naval_fate.py --version

    Options:
      -h --help     Show this screen.
      --version     Show version.
      --speed=<kn>  Speed in knots [default: 10].
      --moored      Moored (anchored) mine.
      --drifting    Drifting mine.

    """
    from docopt import docopt


    if __name__ == '__main__':
        arguments = docopt(__doc__, version='Naval Fate 2.0')
        print(arguments)

Beat that! The option parser is generated based on the docstring above
that is passed to ``docopt`` function.  ``docopt`` parses the usage
pattern (``"Usage: ..."``) and option descriptions (lines starting
with dash "``-``") and ensures that the program invocation matches the
usage pattern; it parses options, arguments and commands based on
that. The basic idea is that *a good help message has all necessary
information in it to make a parser*.

Also, `PEP 257 <http://www.python.org/dev/peps/pep-0257/>`_ recommends
putting help message in the module docstrings.

安装(Installation)
======================================================================

使用 `pip <http://pip-installer.org>`_ 实现简单安装::

    pip install docopt==0.6.2

另外一种安装方式，你只需把``docopt.py``文件拖入你的项目中 --它是独立可用的。

**docopt** 已经经过 Python 2.6, 2.7, 3.3, 3.4, 3.5 和 PyPy 的测试。

测试(Testing)
======================================================================

你可用下列命令运行单元测试:

    python setup.py test

接口(API)
======================================================================

.. code:: python

    from docopt import docopt

.. code:: python

    docopt(doc, argv=None, help=True, version=None, options_first=False)

``docopt`` 需要1个必须和4可选参数:

- ``doc`` 可以是一个文档字符串模块(``__doc__``)，
  或者一些其他包含能被扩展解析器解析创建的 **help message** 字符串。
  如何编写这样的 **help message** 的简单规则，将在接下来的章节中给出。
  这里是一个简单示例:

.. code:: python

    """Usage: my_program.py [-hso FILE] [--quiet | --verbose] [INPUT ...]

    -h --help    show this
    -s --sorted  sorted output
    -o FILE      specify output file [default: ./test.txt]
    --quiet      print less text
    --verbose    print more text

    """

- ``argv`` 是一个可选参数向量；默认情况下，``docopt`` 使用这些参数向量传递给程序的(``sys.argv[1:]``)。
  或者，你也可以提供一个字符串数组，例如 ``['--verbose', '-o', 'hai.txt']``。

- ``help`` 默认为 ``True``，指定解析器是否应自动打印帮助消息（如 ``doc``），然后终止程序，
  如果遇到 ``-h`` 或 ``--help`` 选项（选项应该存在于使用模式中，更多说明在下面）。
  如果要手动处理 ``-h`` 或 ``--help`` 选项（作为其他选项），需设置 ``help=False``。

- ``version`` 默认为 ``None``，是指定程序版本的可选参数。
  如果指定了（假设在使用模式中输入了 ``--version`` 选项），当解析器遇到 ``--version`` 选项，
  它将打印 ``version`` 并终止。
  ``version`` 可以是任何可打印的对象，但多数是字符串，例如 ``"2.1.0rc1"``。

    注意：当 ``docopt`` 设置为自动处理 ``-h``， ``--help`` 和 ``--version`` 选项时，
    你仍然需要在使用模式中输入它们才能生效。此外，也方便为你的用户了解如何使用。

- ``options_first`` 默认为 ``False``。如果设置为 ``True`` 将不允许混序选项和位置参数。
  也就是说在第一个位置参数后，所有内容将被解释为参数，即使看起来像选项。
  这可以严格兼容于POSIX，或者说你想要将参数分派给其他程序。

**return** 的值是一个包含选项，参数和命令作为键的简单字典，准确拼写你的帮助消息。
选项中较长的优先作为键。
例如，如果你调用示例为::

    naval_fate.py ship Guardian move 100 150 --speed=15

返回字典内容将是:

.. code:: python

    {'--drifting': False,    'mine': False,
     '--help': False,        'move': True,
     '--moored': False,      'new': False,
     '--speed': '15',        'remove': False,
     '--version': False,     'set': False,
     '<name>': ['Guardian'], 'ship': True,
     '<x>': '100',           'shoot': False,
     '<y>': '150'}

``Help message`` 格式
======================================================================

**Help message** 由2部分组成:

- ``Usage pattern`` 使用模式，例如::

    Usage: my_program.py [-hso FILE] [--quiet | --verbose] [INPUT ...]

- ``Option descriptions`` 选项说明，例如::

    -h --help    show this
    -s --sorted  sorted output
    -o FILE      specify output file [default: ./test.txt]
    --quiet      print less text
    --verbose    print more text

它们的格式如下所述，其他内容不再累述。

``Usage pattern`` 格式
----------------------------------------------------------------------

**Usage pattern** 是一个用 ``usage:`` *(不区分大小写)* 开头的 ``doc`` 字符串，而且最下面以一个明显空行结束。
这是个小例子:

.. code:: python

    """Usage: my_program.py

    """

``usage:`` 后面的第一个单词作为你的程序名称。你可以多次指定程序名称来表示一系列唯一用法模式:

.. code:: python

    """Usage: my_program.py FILE
              my_program.py COUNT FILE

    """

每个模式可以由以下元素组成:

- **<arguments>**, **ARGUMENTS**. 参数被指定为大写字母，例如： ``my_program.py CONTENT-PATH``
  或者由尖括号包围的词： ``my_program.py <content-path>``。
- **--options**. 选项是由用破折号(``-``)起始的单词，例如：``--output``, ``-o``。
  您可以 "堆叠" 几个单字母选项，例如：``-oiv``，这与 ``-o -i -v`` 相同。
  选项可以有参数，例如：``--input=FILE`` 或 ``-i FILE`` 甚至 ``-iFILE``。
  无论如何，指定选项说明可以具有一个参数，一个默认值，或指定同义选项的短/长单词（请参阅选项说明的下一部分）。
- **commands** 都是不遵循上述 ``--options`` 或 ``<arguments>`` 或 ``ARGUMENTS`` 的约定，
  附加上两个特殊命令：单破折号 "``-``" 和双破折号 "``--``"（见下文）。

使用以下构造来指定模式:

- **[ ]** (中括号) **可选**元素。例如： ``my_program.py [-hvqo FILE]``
- **( )** (小括号) **必选**元素。所有 *未* 放在 **[ ]** 中的元素也是必需的。例如：
  ``my_program.py --path=<path> <file>...`` 与 ``my_program.py (--path=<path> <file>...)`` 相同。
  （注意， "必选选项" 对于使用者来说可能不是一个好用法）。
- **|** (竖线) **互斥**元素。 如果需要互斥元素至少实现一个，则使用 **( )** 分组：
  ``my_program.py (--clockwise | --counter-clockwise) TIME``。
  如果不需要互斥元素都实现，则使用 **[ ]** 对它们进行分组：
  ``my_program.py [--left | --right]``。
- **...** (省略号) **一个或多个**元素。指定可以接受任意数量的重复元素，
  用省略号(``...``)，例如： ``my_program.py FILE ...`` 意思是接受一个或多个 ``FILE``。
  如果想要接受零个或多个元素，请用中括号(``[ ]``)，例如： ``my_program.py [FILE ...]``。
  省略号(``...``)放在表达式的左边作为一个一元运算符。
- **[options]** (区分大小写) 任意选项的快捷方式。
  如果在使用模式的选项描述中要指定可以提供任意已定义的选项，而不想在此都一一申明出来，就可以使用 **[options]**。
- "``[--]``". 双短线 "``--``" 按约定用于来区分位置参数而不会被误认为是选项。要支持这个约定，在你的使用模式中添加 "``[--]``"。
- "``[-]``". 单短线 "``-``" 按约定用于表示 ``stdin`` 而不是文件。要支持这个约定，请在您的使用模式中添加 "``[-]``"。
  "``-``"作为正常命令。

如果你的模式允许匹配几次无参数选项（一个标志）::

    Usage: my_program.py [-v | -vv | -vvv]

那么将计算选项的出现次数。也就是说 ``my_program -vv`` 返回的字典内容中 ``args['-v']`` 等于 ``2``，这同样适用于命令。

如果您的使用模式允许多次匹配同名的选项和选项参数，或同名的位置参数，这些匹配的参数将被收集到一个列表中::

    Usage: my_program.py <file> <file> --path=<path>...

也就是说，调用 ``my_program.py file1 file2 --path=./here --path=./there`` 返回的字典内容将包含
``args['<file>'] == ['file1', 'file2']`` 和 ``args['--path'] == ['./here', './there']``。


Option descriptions format
----------------------------------------------------------------------

**Option descriptions** consist of a list of options that you put
below your usage patterns.

It is necessary to list option descriptions in order to specify:

- synonymous short and long options,
- if an option has an argument,
- if option's argument has a default value.

The rules are as follows:

- Every line in ``doc`` that starts with ``-`` or ``--`` (not counting
  spaces) is treated as an option description, e.g.::

    Options:
      --verbose   # GOOD
      -o FILE     # GOOD
    Other: --bad  # BAD, line does not start with dash "-"

- To specify that option has an argument, put a word describing that
  argument after space (or equals "``=``" sign) as shown below. Follow
  either <angular-brackets> or UPPER-CASE convention for options'
  arguments.  You can use comma if you want to separate options. In
  the example below, both lines are valid, however you are recommended
  to stick to a single style.::

    -o FILE --output=FILE       # without comma, with "=" sign
    -i <file>, --input <file>   # with comma, without "=" sing

- Use two spaces to separate options with their informal description::

    --verbose More text.   # BAD, will be treated as if verbose option had
                           # an argument "More", so use 2 spaces instead
    -q        Quit.        # GOOD
    -o FILE   Output file. # GOOD
    --stdout  Use stdout.  # GOOD, 2 spaces

- If you want to set a default value for an option with an argument,
  put it into the option-description, in form ``[default:
  <my-default-value>]``::

    --coefficient=K  The K coefficient [default: 2.95]
    --output=FILE    Output file [default: test.txt]
    --directory=DIR  Some directory [default: ./]

- If the option is not repeatable, the value inside ``[default: ...]``
  will be interpreted as string.  If it *is* repeatable, it will be
  splited into a list on whitespace::

    Usage: my_program.py [--repeatable=<arg> --repeatable=<arg>]
                         [--another-repeatable=<arg>]...
                         [--not-repeatable=<arg>]

    # will be ['./here', './there']
    --repeatable=<arg>          [default: ./here ./there]

    # will be ['./here']
    --another-repeatable=<arg>  [default: ./here]

    # will be './here ./there', because it is not repeatable
    --not-repeatable=<arg>      [default: ./here ./there]

Examples
----------------------------------------------------------------------

We have an extensive list of `examples
<https://github.com/docopt/docopt/tree/master/examples>`_ which cover
every aspect of functionality of **docopt**.  Try them out, read the
source if in doubt.

Subparsers, multi-level help and *huge* applications (like git)
----------------------------------------------------------------------

If you want to split your usage-pattern into several, implement
multi-level help (with separate help-screen for each subcommand),
want to interface with existing scripts that don't use **docopt**, or
you're building the next "git", you will need the new ``options_first``
parameter (described in API section above). To get you started quickly
we implemented a subset of git command-line interface as an example:
`examples/git
<https://github.com/docopt/docopt/tree/master/examples/git>`_


Data validation
----------------------------------------------------------------------

**docopt** does one thing and does it well: it implements your
command-line interface.  However it does not validate the input data.
On the other hand there are libraries like `python schema
<https://github.com/halst/schema>`_ which make validating data a
breeze.  Take a look at `validation_example.py
<https://github.com/docopt/docopt/tree/master/examples/validation_example.py>`_
which uses **schema** to validate data and report an error to the
user.

Using docopt with config-files
----------------------------------------------------------------------

Often configuration files are used to provide default values which
could be overriden by command-line arguments.  Since **docopt**
returns a simple dictionary it is very easy to integrate with
config-files written in JSON, YAML or INI formats.
`config_file_example.py <examples/config_file_example.py>`_ provides
and example of how to use **docopt** with JSON or INI config-file.


Development
======================================================================

We would *love* to hear what you think about **docopt** on our `issues
page <http://github.com/docopt/docopt/issues>`_

Make pull requests, report bugs, suggest ideas and discuss
**docopt**. You can also drop a line directly to
<vladimir@keleshev.com>.

Porting ``docopt`` to other languages
======================================================================

We think **docopt** is so good, we want to share it beyond the Python
community! All official docopt ports to other languages can be found
under the `docopt organization page <http://github.com/docopt>`_
on GitHub.

If your favourite language isn't among then, you can always create a
port for it! You are encouraged to use the Python version as a
reference implementation.  A Language-agnostic test suite is bundled
with `Python implementation <http://github.com/docopt/docopt>`_.

Porting discussion is on `issues page
<http://github.com/docopt/docopt/issues>`_.

Changelog
======================================================================

**docopt** follows `semantic versioning <http://semver.org>`_.  The
first release with stable API will be 1.0.0 (soon).  Until then, you
are encouraged to specify explicitly the version in your dependency
tools, e.g.::

    pip install docopt==0.6.2

- 0.6.2 Bugfix release.
- 0.6.1 Bugfix release.
- 0.6.0 ``options_first`` parameter.
  **Breaking changes**: Corrected ``[options]`` meaning.
  ``argv`` defaults to ``None``.
- 0.5.0 Repeated options/commands are counted or accumulated into a
  list.
- 0.4.2 Bugfix release.
- 0.4.0 Option descriptions become optional,
  support for "``--``" and "``-``" commands.
- 0.3.0 Support for (sub)commands like `git remote add`.
  Introduce ``[options]`` shortcut for any options.
  **Breaking changes**: ``docopt`` returns dictionary.
- 0.2.0 Usage pattern matching. Positional arguments parsing based on
  usage patterns.
  **Breaking changes**: ``docopt`` returns namespace (for arguments),
  not list. Usage pattern is formalized.
- 0.1.0 Initial release. Options-parsing only (based on options
  description).
