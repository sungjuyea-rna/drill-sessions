# Session 02. Thinking Python in application
As a non-software engineer, you might have used Python in tools like Jupyter Notebook to run a few lines of code to handle simple tasks and data processing. However, when writing a production-level application, you cannot run and check every single chunk of Python code all the time! In this session, we're going to cover Python as an application programming language, not a mere calculation and data-handling tool. 

Before you start this session, [download and install any version of Python above 3](https://www.python.org/downloads/) if you are using Windows. On macOS and Linux, you already have a certain version of Python installed on your machine, so you don't have to install another version of Python separately. In either case, after finishing this session, we won't use this manually installed Python. 

## 1. Running Python on the command line
You may have heard that Python is an *interpreted language*. To explain it briefly, the computer executes each single line of code rather than preparing the entire code ahead of time as a machine-readable sequence of `0`s and `1`s. Such languages are called *compiled languages*. But there are many ways of running a piece of code written in a programming language, so it is not easy to divide them into a dichotomy. For the purpose of this drill session, please remember that Python is a language that the computer executes the code line by line (sometimes people call such a language a *script language*).

But how does the computer execute Python code without any information about how it is translated into machine-readable code? Thus we have *Python interpreters* which help the computer to interpret Python code and execute it. The standard implementation of a Python interpreter is [CPython](https://github.com/python/cpython) which was created by [Guido van Rossum](https://github.com/gvanrossum), the creator of Python.

### Exercises
In fact, installing Python means installing a Python interpreter. Now we will run Python using the interpreter directly, rather than through convenient GUI applications such as Jupyter Notebook.

1. Open your terminal just like in the previous session, and run either `python` or `python3`. Then you may see the following texts:

```sh
Python 3.13.4 (main, Jun  4 2025, 17:33:37) [Clang 20.1.4 ] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

Type `import this` and press Enter. What do you see? 
Answer:

2. The above interface represented by the `>>>` symbol in the terminal is what we call "REPL (Read-Eval-Print Loop)" environment. You can write and execute a single line of Python code directly and see the result immediately. For sure, this is a good tool for simple execution and debugging, but not for some bigger applications. Type `quit` and press Enter to exit the environment. 

Now we want to run a single Python code using the interpreter itself. But before then, let's check the version of the current Python interpreter. 

Run `python -V` or `python3 -V` in the terminal. What do you see?
Answer:


3. For the conciseness of the explanation, we will use `python` instead of `python3`. If `python` doesn't work on your machine, run `python3` instead of `python`. The other parts of each command should not be changed. 

Next, run `python -c "import os; print(os.getcwd())"`. What do you see?
Answer:

`python` with the `-c` flag indicates the interpreter that the following string should be interpreted as Python code to be executed.

## 2. Thinking Python code at several levels
So far we have looked into how to run Python code using an interpreter directly. We have already covered how to run a single line of Python code in the terminal (actually it was of two lines, separated by `;`). Now let us ask this question. How do we run Python code with multiple lines? 

Then here comes the notion of [Python *module*](https://docs.python.org/3/tutorial/modules.html). 

### Exercises
If you want to know further, we recommend reading this [tutorial page on Python modules](https://docs.python.org/3/tutorial/modules.html) right before section 6.1 and the short introduction of section 6.4. For now we think it is sufficient to know what modules and packages are and how they are connected by `import`. 

1. Create a Python module
To be honest, *module* in Python is a fancy name for a single Python file with the extension `.py` (but note that in other languages the term *module* could mean something different). Create a module named `zen.py` under `session-02/`, and write the following code:

```py
import this

print("\n\nThis is Zen of Python!")
```

Now run `python zen.py`. What do you see?
Answer:

2. Run a Python module selectively
As you saw in the previous exercise, running a module executes all the lines in the file. However, on many occasions we only want to run certain logic depending on the current situation. Hence, it is always recommended to wrap the code in functions and classes within a module.

We have a module named `print_today.py` in the directory `session-02/`, where the code looks like this:

```py
def print_today() -> str:
    # TODO: write your code here
    raise NotImplementedError()


if __name__ == "__main__":
    print_today()

```

Remove `NotImplemented` exception and implement the function. (*Hint*: check out the [datetime](https://docs.python.org/3/library/datetime.html) standard library)

Note that we now use the `if __name__ == "__main__"` condition check, which means the line `print_today()` will run only if the module `print_today.py` is directly run by the interpreter, not when the module is imported into another module. 

3. Combine a collection of modules to create a Python package
Theoretically, you could write all your logic in a single module, but that is not desirable from the perspective of reusability and maintenance. "Where has this error come from?" "Why is this logic kept repeated throught the entire module?" We need to *separate* the parts that are logically separable so that we can organize the code. 

In Python, a package is a collection of modules that are combined in a structured way. The modules are usually organized in a hierarchical file system. Each directory contains the file `__init__.py` which indicates that the directory is a Python (sub)package.

Look at the package called `calculator` under `session-02/`. The structure of the package is like this:

```sh
calculator
├── __init__.py
├── add.py
├── mul.py
└── variadic
    ├── __init__.py
    ├── add.py
    └── mul.py
```

Once you briefly skim through the structure of the package, look at the `# TODO`s in the `run_calculator.py` module under the same `session-02/` and follow the instructions there. What modules and functions do you need to import? 

## 3. Dependencies, Project, and virtual environment
Now you get the idea of how to organize your logic written in Python at a bigger scale. But in almost all cases, you may have to `import` other logic that someone else has written. You might already have used popular Python packages such as [numpy](https://numpy.org/) and [pandas](https://pandas.pydata.org/), to name a few. The external Python packages that you will use in your application are called *dependencies*. However, managing dependencies is difficult. Think about the following two issues.

1. How do you add your dependencies? You may add them manually, but that process is quite complicated in Python. Also, if you want to share your work to someone else, then would you send those heavy dependencies (sometimes containing huge binaries written in C/C++ or Rust) all together, which might not work at all on someone else's machine due to the different hardware or OS?

2. There is another issue. Say you have two different Python applications that are being developed separately, and both of them need `numpy`. You can add the package in your machine and make two different app share it. However, what if one of the application uses another package that only works under version 2.3, but your *global* numpy package is version 2.4?

### `pyproject.toml` and dependencies
In order to handle these issues and some more, people isolate logically separable Python modules as a package and manage it as a *project*. The term *project* here doesn't have much meaning in terms of programming language. It just means that this package is managed by someone with some purposes. 

In modern Python, a Python project is managed by a declarative text file called [`pyproject.toml`](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/). You don't have to know the details of it right now, but just keep in mind that any Python project needs this file and you can find the information about the project such as dependencies. 

Once you create a project and want to add a dependency package, you need to mention it in `pyproject.toml`. There are various ways of specifying a dependency in the file, so let us introduce it in a later section when we use [uv](https://docs.astral.sh/uv/).

Now you can add your dependencies either by typing them in `pyproject.toml`, or by simply using tools such as uv and running a single line of command. In this way, you can simply share the information of your project and even enable someone else to install the dependencies on their machine without having to receive the heavy scripts and binaries on your machine.

### Virtual environment
However, once you add a dependency, where does it go? How can you make sure that the dependency only works in the current project and completely separated from the others? In a Python project, you create the *virtual environment* of the project, which is a huge directory under the project directory, and all the dependencies for this project go into it. You can put a custom name on it, but conventionally people simply name it `venv` or `.venv`. Here in this drill we would like to use `.venv`.

But what is actually a virtual environment? Conceptually, it is a separate running environment that is completely separated from the other virtual environments. You can add or delete dependencies but that doesn't affect anything in the other virtual environment. 

Now let's do some coding. 

### Exercises
1. Add a file named `pyproject.toml` under the directory of the package `calculator` that we used in the previous exercise. The content of the file is as follows:

```py
[project]
name = "calculator"
version = "0.1.0"
description = "Add your description here"
requires-python >= "3.12" # change this to your current python version
dependencies = [] # this is where your dependencies are added
```

2. Next, create the virtual environment for the package. Here we will use [`venv` standard library](https://docs.python.org/3/tutorial/venv.html). Run the following command under `calculator`: `python -m venv .venv`. 

3. Finally, you add a dependency in the virtual environment. But before that, you need to make the current Python interpreter recognize that you're in the virtual environment. 

Run the following command first: `source .venv/bin/activate`. You'll see that there is an additional string `(.venv)` in your terminal. That indicates your Python interpreter will work within the current virtual environment. 

And then you specify the dependency inside the `dependencies` array in the `pyproject.toml` file, as follows:

```toml
dependencies = ["numpy"]
```

Now add the dependency by running the following command `pip install .` (or `pip3 install numpy .` if `pip` doesn't work on your machine). From the message on your terminal, you will get the version of `numpy` that is just added. What is the version of your numpy?
Answer:

## 4. Introducing UV - a tool for managing an entire Python project
As you saw in the previous exercise, you have to go through several steps to bootstrap a single Python project. For sure, it is a lot of work, and people have invented *dependency management tools* for Python, such as Pipenv, Poetry, or PDM, to name a few. Among those, the most popular tool these days is obviously [uv](https://docs.astral.sh/uv/). uv is actually more than a simple dependency management tool. You can also install and pin the version of the Python interpreter for a specific project! So from now on, once you install uv, then you won't ever need to install Python separately.

### Exercises
First, [install uv](https://docs.astral.sh/uv/getting-started/installation/). Once you install it by running the installation script, then follow the steps below:

1. Create a new project with the name you want. This project needs to be running on Python 3.14. How would you set your Python version solely for this project?   

Hint: Reference the following pages:
- https://docs.astral.sh/uv/guides/projects/
- https://docs.astral.sh/uv/reference/cli/#uv-init--python

2. Next, create a virtual environment *manually*, but this time using uv. 
Reference this page: https://docs.astral.sh/uv/pip/environments/

3. Now, make a single fun function and run it in `main.py` that is generated by default when you created the project.

However, please note that:
- You have to create one subpackage inside the project. That is, a directory with `__init__.py` and other Python modules if necessary.
- You have to install one single third party package such as `pandas`. There is no limit to the choice of package, but make sure it is not too heavy, things like Pytorch or LLM-related libraries. Also, don't forget to activate the virtual environment!
- You have to import a single function that is written in the sub package inside `main.py`, and run it. Make sure you use the `if __name__ == "__main__"` clause.

## Conclusion
Now we just kicked off our journey to Python as a software language, not a mere automation tool or data analysis scripting language. From the next time, sessions will be more focused on **building** actual projects, which means things will be more fun.  
