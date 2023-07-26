# Python string_adder kata

I've created this kata with the objective of practicing using a [Repository Pattern](https://www.cosmicpython.com/book/chapter_02_repository.html) in Python.
The aim is for the kata to be doable in less than half a day, ideally ~2h.

Feedback is always welcome, it's my first time actually creating such an exercise, and I'm sure it could be improved in a lot of ways !

Feel free to copy it, modify it, use it and more generally do anything you want with it.

# The kata

## Context

Imagine you're a developper who works in a company that uses Python.
Some day, your boss asks you to write a small CLI tool for you and your dev colleagues.
The functional requirements will start simple, but your boss anticipates more complexity to come as time goes, so he asks you to code it up with evolution and maintainability in mind !

Basically, this CLI tool, called `string_adder`, takes one or more text file paths as inputs and adds a string as a footer to those files.
The string added at the end consists in the following footer that your company is legally required to add at the end of all their text files:

"# 2023, copyright My-Awesome-Company owned by Leader McBoss"

## The first implementation

The boss asks you for a first MVP: a Python scrpt that takes the text-file paths as CLI-arguments, like so:
```bash
python string_adder.py absolute/path/to/file/1.txt absolute/path/to/file/2.py
```

## A more convenient interface

The whole team (and the boss !) are all very satisfied by your tool... except that they find it quite cumbersome to use.
The target of their complaints is that all the files must be passed as arguments each time you call the `string_adder`, which can quickly get unwieldy when you have lots of files !

As a result, they ask you to add a new feature to the `string_adder`: it would be super nice if the text-file paths could be specified in a special `.string_adder` file.
That way, they can easily maintain a long list of files ! They can even version it !

Of course, not everyone requires this new feature so we must still be able to pass arguments through the CLI directly.

The `.string_adder` file would simply contain one text-file path per line, like in the following example:
```bash
absolute/path/to/file/1.txt
absolute/path/to/file/2.py
absolute/path/to/file/3
(...)
```

You can assume that the `.string_adder` file lives next to the `string_adder.py` file.

## A first issue

The legal team, hearing about your improvements, comes to see you. They're worried: legally your tool should add the footer once and only once at the bottom of the file !
But what happens if the same file is specified both in the `.string_adder` file and through the CLI ? Or erroneously passed twice through the CLI ?

Can you show them a test to reassure them ? (and modify your implementation if it doesn't fit this behavior, of course)

You can assume that the colleagues using your tool only use it on files that do not have the footer yet: no need to check the last line of the file for the footer's presence.

## Integration with Python projects

Most of your colleagues actually work on Python projects. As such, they already use a `pyproject.toml` file to specifiy the behavior of their CLI tools like Black, Ruff, isort, ...
They don't really appreciate having the add a `.string_adder` file that clutters their Git repositories.

They ask you if you could modify the tool so it picks up the list of text-file paths from a `files` array in a `[tool.string_adder]` section of the `pyproject.toml` file as well.
```toml
[tool.string_adder]
files = [
    "absolute/path/to/file/1.txt",
    "absolute/path/to/file/2.py",
    "absolute/path/to/file/3",
]
```
Of course, since not all teams use a `pyproject.toml` file, this means you have to add this __on top__ of the existing ways to pass files.

## Check yo'self before you wreck yo'self

A few days later, one colleague comes by. He's happy about the tool, but his team uses all 3 input methods (CLI arguments, `.string_adder` file and `[tool.string_adder]` section in their `pyproject.toml` file). As a result, he has trouble having a unified view of which files are targetted by the `string_adder`.

He asks you to add a "check mode": running the `string_adder` in this mode returns the full list of all text-file paths that would get modified, but without actually modifying them !
He suggests the use of an environment variable to activate this mode: the `string_adder` should be in this mode only when the `STRING_ADDER_CHECK` variable is set (whatever its value).

## Anticipating the end of the year

Everyone is happy using your tool, but you realise that 2023 is coming to an end, so the footer message is about to be obsolete !

You decide to anticipate the issue: the string added as footer will stay the one described above by default (it is the spec you got after all!), but you want to let teams can override this by setting the `STRING_ADDER_FOOTER_STRING` environment variable. When it is set, the footer is set to this environment variable's value.

## Legal team uses Excel !

Hearing about your "check mode", the legal team is super interested ! They actually need the list of files to which the `string_adder` added a footer to include it as an appendix to their Quarterly report, but they work with Excel.

Can you modify the `string_adder` so it outputs a CSV file when used in check mode, but only if the `STRING_ADDER_CSV` environment variable is also set ?

## Future plans

Between your boss, the Legal team and your colleagues, there are quite a lot more ideas to add features and complexity to the tool !
You can think about how would you change your code to accomodate the following requests:
- What if we wanted to specify the "check mode" or the footer string through the CLI (add `--check` as argument) or through the `pyproject.toml` file (with a `check = true` line) instead of using env vars ?
- What about detecting if a file already ends with the footer, and outputting a warning in that case ? We could also add a "force mode" to bypass this check and add the footer anyway.
- Some colleagues are fond of specifying their config in JSON, other would like to use YAML. How would you go about catering to those requests ?
