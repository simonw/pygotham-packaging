# pygotham-packaging

Notes from my presentation on Python packaging at PyGotham 2021

## Packaging a single module

I started out by creating a new `pids` package using [this code](https://github.com/CAVaccineInventory/vial/blob/main/vaccinate/core/baseconverter.py), which I have copied-and-pasted into several different projects over the years.

1. `mkdir pids && cd pids`
2. Create `pids.py` in that directory with the contents of [this file](https://github.com/CAVaccineInventory/vial/blob/main/vaccinate/core/baseconverter.py)
3. Create a new `setup.py` file in that folder containing the following:
    ```python
    from setuptools import setup

    setup(
        name="pids",
        version="0.1",
        description="A tiny Python library for generating public IDs from integers",
        author="Simon Willison",
        url="https://github.com/simonw/...",
        license="Apache License, Version 2.0",    
        py_modules=["pids"],
    )
    ```
4. Run `python3 setup.py sdist` to create the packaged source distribution, `dist/pids-0.1.tar.gz`

## Testing it in a Jupyter notebook

Having created that file, I demonstrated how it can be installed in a Jupyter notebook by running the following in a notebook cell:

    %pip install /Users/simon/Dropbox/Presentations/2021/pygotham/pids/dist/pids-0.1.tar.gz

Having done this, I could excute the library like so:

    >>> import pids
    >>> pids.pid.from_int(1234)
    'gxd'

## Uploading the package to PyPI

I used [twine](https://pypi.org/project/twine/) (`pip install twine`) to upload my new package to [PyPI](https://pypi.org/). I had to paste in my PyPI account's username and password:
```
% twine upload dist/pids-0.1.tar.gz
Uploading distributions to https://upload.pypi.org/legacy/
Enter your username: simonw
Enter your password: 
Uploading pids-0.1.tar.gz
100%|██████████████████████████████████████| 4.16k/4.16k [00:00<00:00, 4.56kB/s]

View at:
https://pypi.org/project/pids/0.1/
```
The release is now live at https://pypi.org/project/pids/0.1/ - and anyone can run `pip install pids` to install it.

## Adding a README

I added a `README.md` file [containing this](https://raw.githubusercontent.com/simonw/pids/0.1.2/README.md). Then I modified my `setup.py` file to look like this:
```python
from setuptools import setup
import os

def get_long_description():
    with open(
        os.path.join(os.path.dirname(__file__), "README.md"),
        encoding="utf8",
    ) as fp:
        return fp.read()

setup(
    name="pids",
    version="0.1.1",
    long_description=get_long_description(),
    long_description_content_type="text/markdown",
    description="A tiny Python library for generating public IDs from integers",
    author="Simon Willison",
    url="https://github.com/simonw/...",
    license="Apache License, Version 2.0",    
    py_modules=["pids"],
)
```
Note that I updated the version number too.

Running `python3 setup.py sdist` created a new file called `dist/pids-0.1.1.tar.gz` - I then uploaded that file using `twine upload dist/pids-0.1.1.tar.gz` which created a new release with a visible README at https://pypi.org/project/pids/0.1.1/

## Adding some tests

I like using [pytest](https://docs.pytest.org/) for tests, so I added that as a test dependency by modifying `setup.py` to add the following two lines:

```python
    extras_require={"test": ["pytest"]},
    tests_require=["pids[test]"],
```
Next, I created a virtual environment and installed my package and its test dependencies into it in "editable" mode like so:

```
python3 -m venv venv
source venv/bin/activate
pip install -e '.[test]'
```
Now I can run the tests!

```
(venv) pids % pytest
============================= test session starts ==============================
platform darwin -- Python 3.9.6, pytest-6.2.5, py-1.10.0, pluggy-1.0.0
rootdir: /Users/simon/Dropbox/Presentations/2021/pygotham/pids
collected 0 items                                                              

============================ no tests ran in 0.01s =============================
```
There aren't any tests yet. I created a `tests/` folder and then dropped in a `test_pids.py` file that looked like this:
```python
import pytest
import pids

def test_from_int():
    assert pids.pid.from_int(1234) == "gxd"

def test_to_int():
    assert pids.pid.to_int("gxd") == 1234
```
Running `pytest` in the project directory now runs those tests:
```
(venv) pids % pytest
============================= test session starts ==============================
platform darwin -- Python 3.9.6, pytest-6.2.5, py-1.10.0, pluggy-1.0.0
rootdir: /Users/simon/Dropbox/Presentations/2021/pygotham/pids
collected 2 items                                                              

tests/test_pids.py ..                                                    [100%]

============================== 2 passed in 0.01s ===============================
```
