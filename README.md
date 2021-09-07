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
