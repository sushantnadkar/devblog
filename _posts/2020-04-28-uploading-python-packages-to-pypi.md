---
layout: post
title: How to upload Python code to PyPi
date: 2020-04-28
---
Today in this post I will be talking on how to upload your Python code to Python Package Index (PyPi) so that other developers around the world can benefit from the code you wrote.

PyPi is a software repository for Python programming language. This a similar to Git repositories, except that PyPi is exclusively for Python packages. Packages uploaded here are installed using **pip** a Python package installer. So, let's move ahead and let me tell you how you can let other developers make use of the code you have written.

So, let's get started...

## Creating the package
I am assuming that you already have some code files ready to be distributed. Apart from the code files we also need some specific files at specific locations. Following is the suitable file structure:

```
package_name
├── module_1
│  ├── __init__.py
│  └── other_py_files.py
├── module_2
│  ├── __init__.py
│  └── other_py_files.py
├── README.md
├── LICENSE
└── setup.py
```

In the above structure:

- `__init__.py`: these can be empty file, but they need to be present in all the directories that you wish to be imported in python code, using `import module_1`. Hence, `module_1` and  `module_2` can be imported.
- `LICENSE`: it is advisable that you include a license. If you are not sure as to which license to include you can have a look [Choose an open source license](https://choosealicense.com/).
- `README.md`: do add a readme, giving enough info related to your package. This help others figure out if a package is suitable for their needs and also an idea as to how to use the code.
- `setup.py`: this is a build script required by `setuptools`, for now create this file, we will add content in the following section.

## The setup file
`setup.py` is a build script, that contains all the necessary information needed for [setuptools](https://packaging.python.org/key_projects/#setuptools) for installing the package. Open the `setup.py` file and add the following content:

```python
with open("README.md", "r") as fh:
   long_description = fh.read()

setuptools.setup(
    name="package_name", # Replace with your own username
    version="0.0.1",
    author="Example Author",
    author_email="author@example.com",
    description="A small example package",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/pypa/sampleproject",
    packages=setuptools.find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires='>=3.6',
)
```

Let me explain the above content. 

The `setuptools.setup()` function takes several arguments, some of the basic one are mentioned here.

- `name`: this will be the name of the package. Make sure that it contains only alphabets, numbers, hyphen ("-") and underscore ("_"). Also that it should be unique and not be taken up by other packages on PyPi.
- `version`: this will the version number for your package. Have a look at, [PEP 440 -- Version Identification and Dependency Specification](https://www.python.org/dev/peps/pep-0440/) for more details on version numbers.
- `author`: name of the author of the package.
- `author_email`: contact email of the author of the package.
- `description`: one liner description of the package.
- `long_description`: detailed description of the package. This will be displayed on the package description page on PyPi. Generally this is loaded from a file. The following lines form above snippet are used to load the content from a file:

	```python
	with open("README.md", "r") as fh:
	    long_description = fh.read()
	```

	Most popular file types used are markdown (`md`), reStructured text (`rst`) or plain text (`txt`).
- `long_description_content_type`: type of the file/content provided to `long_description`. Options are `text/plain`, `text/x-rst`, or `text/markdown` for plain text, restructured or markdown files respectively.
- `url`: url to project's homepage
- `packages`: list of all other Python packages required by your package. To make your life easier, you don't need to list them manually, use the `setuptools.find_packages()` function, this will list all the packages and sub-packages used.
- `classifiers`: provides pip and PyPi some metadata related to our package. In the above example it states that it requires Python 3, has a MIT license and is OS independent. For full list of classifiers head off to, [https://pypi.org/classifiers/](https://pypi.org/classifiers/).
- `python_requires`: mentions the Python version required by your package.

The arguments mentioned here provides some basic information about the package. If you wish to know all the arguments you can have a look at [What keyword arguments does `setuptools.setup` accept](https://stackoverflow.com/questions/58533084/what-keyword-arguments-does-setuptools-setup-accept).

## Generating distribution packages 
Now that our project structure and setup file is ready, let's move on and build our project into distribution packages to be uploaded.

I assume that you already have `setuptools` and `wheels` installed and updated. If not use the following code to do so:

```bash
python3 -m pip install --upgrade setuptools wheel
```

If you have trouble doing so, have a look at [Installing Packages](https://packaging.python.org/tutorials/installing-packages/).

Now, navigate to the root directory of your project, that is, to the directory where we have our `setup.py` file, and execute the following command:

```bash
python3 setup.py sdist bdist_wheel
```

If you have trouble creating these packages, then you can report it by creating a Github issue at [Packaging Problems](https://github.com/pypa/packaging-problems/issues/new?title=Trouble+following+packaging+libraries+tutorial).

After successful execution of this code a new directory will be creating with following files in it:

```bash
dist/
  package_name-0.0.1-py3-none-any.whl
  package_name-0.0.1.tar.gz
```

You will have two files in `dist` directory at the root of your project:

- `.tar.gz`: source file, this is nothing but compressed raw source code ready to be distributed
- `.whl`: distribution package, this contains metadata and all files ready to be moved to specific location on the target machine, for your package to be installed


## Uploading the files
Now that we have our package ready to be distributed, let's upload them!

### Upload Test
Yes! you read it right. We will test uploading of our package first. And no, we won't be uploading our test package on PyPi's live servers, and make a mess. PyPi have made sure of that. Along with their [live server](https://pypi.org) they also maintain a [test server](https://test.pypi.org/).

So go ahead log onto their test server and [register](https://test.pypi.org/account/register/) yourself.

One more thing, that we need before we can upload the files. Use the following code to install twine. Twine is the primary and official tool for developers to upload files to PyPi:

```bash
python3 -m pip install --upgrade twine
```

Now we have everything set-up to upload our files, using the following code:

```bash
python3 -m twine upload --repository testpypi dist/*
```

In the previous command, `--repository` is used to mention that we are uploading our package to test server and not to live server.

Type in your username and password when you will be prompted for. When you do so your file should get uploaded and you should see the following:

```bash
Uploading distributions to https://test.pypi.org/legacy/
Enter your username: [your username]
Enter your password:
Uploading package_name-0.0.1-py3-none-any.whl
100%|█████████████████████| 4.65k/4.65k [00:01<00:00, 2.88kB/s]
Uploading package_name-0.0.1.tar.gz
100%|█████████████████████| 4.25k/4.25k [00:01<00:00, 3.05kB/s] 

View at:
https://test.pypi.org/project/package_name/0.0.1/
```

You can now view your project on PyPi test server. Head off, to the url mentioned at the end of the previous command.

### Verify the upload

Now that we have uploaded our package, let's install this directly for PyPi server using pip and verify our upload. Use the following code to do so:

```bash
python3 -m pip install --index-url https://test.pypi.org/simple/ --no-deps package_name
```

Let me explain you the options used in previous command:

- `--index-url`: to mention that we are installing from test server and not from live PyPi server
- `--no-deps`: this tells pip to not install any dependencies (if any) for the package. And that is because, data on the test server can be purged anytime without notice, as a result, installing dependencies may fail or may install something unexpected. It is always recommended to use `--no-deps` while installing anything from PyPi test server.

Use should see the following output for the previous command, indicating successful download and installation of your package:

```bash
Collecting package_name
  Downloading https://test-files.pythonhosted.org/packages/.../package_name-0.0.1-py3-none-any.whl
Installing collected packages: package_name
Successfully installed package_name-0.0.1
```

**Note: Never rely on PyPi test server, all packages along with user accounts from this server is occasionally deleted and purged.**

Congratulation, you have packaged and uploaded your Python project!

### Uploading to and Installing from Live Server

Now, to upload your package to live server, you have to follow same steps that you performed to upload your package to test server, but with the following important modifications:

- Choose a memorable and unique name for you package.
- Register an account on live PyPi server at [https://pypi.org](https://pypi.org) -- note that live and test servers are two different servers, hence no details are shared among these two servers.
- To upload the package to live server you don't need to mention `--repository`. Just use the following, this will upload your package to https://pypi.org, by default:

	```bash
	python3 -m twine upload dist/*
	```
- Similarly, you don't need to mention `--index-url` while installing the package, just use the following:

	```bash
	python3 -m pip install package_name
	```

Remember, you also need to upload your packages on live PyPi server, even if you have uploaded your packages on test server. As the name suggest, it is a test server and is meant only for testing. All packages, including user account details are occasionally deleted. So, after testing and verifying everything on test server, upload your packages on **LIVE** server at [PyPi.org](https://pypi.org/).
