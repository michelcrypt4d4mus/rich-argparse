# rich-argparse-plus

Format **argparse** help output with [**rich**](https://pypi.org/project/rich). This is a fork of the awesome [rich-argparse](https://github.com/hamdanal/rich-argparse) that adds a few features:

1. **Select from many [preconfigured color themes](#perusing-themes)**.
1. **Render to various image/web formats by setting an variable when you run `--help`.** PNG, PDF, HTML, SVG, PS, EPS, colored text are supported. Show off your fancy stuff.
1. **Displays default argument values by default.**
1. **Displays the range of acceptable values for integer arguments limited by `choices=range(n)`**.

**Attn: PyPi Users:** To see the images [view this README on github](https://github.com/michelcrypt4d4mus/rich-argparse-plus/)

![prince](doc/themes/python_-m_rich_argparse_help_prince_theme.png)

(That's the `prince` theme, for obvious reasons).

## Installation

```sh
pip install rich-argparse-plus

# To render to image formats like PNG or PDF you need the optional dependencies:
pip install rich-argparse-plus[optional]
```

## Usage

Pass the `formatter_class` to the argument parser, optionally choosing a theme.
```python
import argparse
from rich_argparse_plus import RichHelpFormatterPlus

RichHelpFormatterPlus.choose_theme('prince')
parser = argparse.ArgumentParser(..., formatter_class=RichHelpFormatterPlus)
```

### Rendering Help Text As Imagery
Formats supported are `html`, `png`, `pdf`, `ps`, `svg`, `eps`, and `txt` (colored text). To actually render send the `RENDER_HELP_FORMAT` environment variable while you run your program with `--help`:

```bash
# Render a png to the current directory
RENDER_HELP_FORMAT=png my_awesome_program.py --help

# Set RENDER_HELP_OUTPUT_DIR to send the output somewhere else
RENDER_HELP_FORMAT=pdf RENDER_HELP_OUTPUT_DIR=doc/themes/ my_awesome_program --help
```


### Perusing Themes
You can view images of all the themes [here in the repo](doc/themes/). Alternatively you can run `rich_argparse_plus_show_themes` to print them to your terminal.

Here's a couple of them:

##### **mother_earth**
![morning_glory](doc/themes/python_-m_rich_argparse_help_mother_earth_theme.png)

##### **the_pink**
![the_pink](doc/themes/python_-m_rich_argparse_help_the_pink_theme.png)

##### **black_and_white**
![black_and_white](doc/themes/python_-m_rich_argparse_help_black_and_white_theme.png)

##### **morning_glory**
![morning_glory](doc/themes/python_-m_rich_argparse_help_morning_glory_theme.png)

##### **dracula**
![dracula](doc/themes/python_-m_rich_argparse_help_dracula_theme.png)

##### **grey_area**
![default](doc/themes/python_-m_rich_argparse_help_grey_area_theme.png)


#### The Random Theme Stream Channel

Try running `rich_argparse_plus_random_theme_stream`.


## Recipes

`RichHelpFormatterPlus` is the equivalent to `argparse.HelpFormatter`. *rich-argparse* also defines
four subclasses of `RichHelpFormatterPlus` that are equivalent to the other argparse formatters:

* `argparse.RawDescriptionHelpFormatter` -> `RawDescriptionRichHelpFormatter`
* `argparse.RawTextHelpFormatter` -> `RawTextRichHelpFormatter`
* `argparse.ArgumentDefaultsHelpFormatter` -> `ArgumentDefaultsRichHelpFormatter`
* `argparse.MetavarTypeHelpFormatter` -> `MetavarTypeRichHelpFormatter`

For more information on what these formatters do, check the [argparse documentation](
https://docs.python.org/3/library/argparse.html#formatter-class).

## Output styles

The default styles used by *rich-argparse* formatters are carefully chosen to work in different
light and dark themes. If the these styles don't suit your taste, read below to know how to change
them.

> **Note**
> Any subsequent mention of `RichHelpFormatter` in this section also applies to its subclasses.

### Customize the colors
You can customize the colors in the output by modifying the `styles` dictionary on the formatter
class. By default, `RichHelpFormatter` defines the following styles:

```python
{'argparse.args': 'cyan',
 'argparse.groups': 'dark_orange',
 'argparse.help': 'default',
 'argparse.metavar': 'dark_cyan',
 'argparse.syntax': 'bold',
 'argparse.text': 'default'}
```

For example, to make the description and epilog *italic*, change the `argparse.text` style:

```python
RichHelpFormatter.styles["argparse.text"] = "italic"
```

### Customize group name formatting
You can change how the names of the groups (like `'positional arguments'` and `'options'`) are
formatted by setting the `RichHelpFormatter.group_name_formatter` function. By default,
`RichHelpFormatter` sets the function to `str.upper` but any function that takes the group name
as an input and returns a str works. For example, to apply the *Title Case* format do this:

```python
RichHelpFormatter.group_name_formatter = str.title
```

### Special text highlighting

You can highlight patterns in the help text and the description text of your parser's help output
using regular expressions. By default, `RichHelpFormatter` highlights patterns of
`--options-with-hyphens` using the `argparse.args` style and patterns of ``back tick quoted text``
using the `argparse.syntax` style. You can control what patterns get highlighted by modifying the
`RichHelpFormatter.highlights` list.
For example, to disable all highlights, you can clear this list using
`RichHelpFormatter.highlights.clear()`.

You can also add custom highlight patterns and styles. The following example highlights all
occurrences of `pyproject.toml` in green.

```python
# 1dd a style called `pyproject` which applies a green style (any rich style works)
RichHelpFormatter.styles["argparse.pyproject"] = "green"
# Add the highlight regex (the regex group name must match an existing style name)
RichHelpFormatter.highlights.append(r"\b(?P<pyproject>pyproject\.toml)\b")
# Pass the formatter class to argparse
parser = argparse.ArgumentParser(..., formatter_class=RichHelpFormatter)
...
```

### Colors in the `usage`

`RichHelpFormatter` colors the usage generated by the formatter using the same styles to color the
arguments and their metavars. If you use a custom `usage` text in the parser, this text will not be
colored.

## Working with subparsers

If your code uses *argparse*'s subparsers and you want to format the subparsers' help output with
*rich-argparse*, you have to explicitly pass `formatter_class` to the subparsers since subparsers
do not inherit the formatter class from the parent parser by default. You have two options:

1. Create a helper function to set `formatter_class` automatically:
   ```python
    subparsers = parser.add_subparsers(...)

    def add_parser(*args, **kwds):
        kwds.setdefault("formatter_class", parser.formatter_class)
        return subparsers.add_parser(*args, **kwds)

    p1 = add_parser(...)
    p2 = add_parser(...)
   ```
1. Set `formatter_class` on each subparser individually:
   ```python
    subparsers = parser.add_subparsers(...)
    p1 = subparsers.add_parser(..., formatter_class=parser.formatter_class)
    p2 = subparsers.add_parser(..., formatter_class=parser.formatter_class)
   ```


## Working with third party formatters

`RichHelpFormatter` can be used with third party formatters that do not rely on the **private**
internals of `argparse.HelpFormatter`. For example, [django](https://pypi.org/project/django)
defines a custom help formatter that is used with the built in commands as well as with extension
libraries and user defined commands. To use *rich-argparse* in your *django* project, change your
`manage.py` file as follows:

```diff
diff --git a/my_project/manage.py b/my_project/manage.py
index 7fb6855..5e5d48a 100755
--- a/my_project/manage.py
+++ b/my_project/manage.py
@@ -1,22 +1,38 @@
 #!/usr/bin/env python
 """Django's command-line utility for administrative tasks."""
 import os
 import sys


 def main():
     """Run administrative tasks."""
     os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'my_project.settings')
     try:
         from django.core.management import execute_from_command_line
     except ImportError as exc:
         raise ImportError(
             "Couldn't import Django. Are you sure it's installed and "
             "available on your PYTHONPATH environment variable? Did you "
             "forget to activate a virtual environment?"
         ) from exc
+
+    from django.core.management.base import BaseCommand, DjangoHelpFormatter
+    from rich_argparse_plus import RichHelpFormatterPlus
+
+    class DjangoRichHelpFormatter(DjangoHelpFormatter, RichHelpFormatterPlus):  # django first
+        """A rich-based help formatter for django commands."""
+
+    original_create_parser = BaseCommand.create_parser
+
+    def create_parser(*args, **kwargs):
+        parser = original_create_parser(*args, **kwargs)
+        parser.formatter_class = DjangoRichHelpFormatter  # set the formatter_class
+        return parser
+
+    BaseCommand.create_parser = create_parser
+
     execute_from_command_line(sys.argv)


 if __name__ == '__main__':
     main()
```

Now try out some command like: `python manage.py runserver --help`. Notice how the special
ordering of the arguments applied by django is respected by the new help formatter.
