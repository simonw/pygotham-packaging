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
