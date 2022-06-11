---
title: "Distributions vs packages in Python"
excerpt: How to distinguish between these terms and choose their names.
date: 2022-05-21 23:00:00 +0300
last_modified_at: 2022-06-11 17:30:00 +0300
toc: true
categories:
- Blog
tags:
- Python
- Packaging
---

## Motivation
Now I am gathering information and planning a series of articles dedicated to Python packages development and while preparing I encounter some interesting facts that I hadn't thought about before.  
One of them is the ambiguity in terminology for distributions and packages in Python and their naming.  
So I decided to share some thoughts about it.

 
## Terminology
**Package**  
If we try to search for _"package"_ definition, we can find in [The Hitchhiker’s Guide to Packaging](https://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/glossary.html#term-package) that among others it is:
> A directory containing an \_\_init\_\_.py file ..., and also usually containing modules (possibly along with other packages).

Probably this is not complete defintion because without \_\_init\_\_.py file it is still can be used as a [namespace package](https://packaging.python.org/en/latest/guides/packaging-namespace-packages/) and there is a great [article](https://bastien-antoine.fr/2022/01/discovering-python-namespace-packages/) about that.

Moreover, we also can read in [Python documentation's glossary](https://docs.python.org/3/glossary.html#term-package) (and it is also discussed in the article above) that there is a little difference between packages and modules:
> Technically, a package is a Python module with an \_\_path\_\_ attribute.

**Distribution**  
For _"distribution"_ definition we can find useful note in the same glossary of [The Hitchhiker’s Guide to Packaging](https://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/glossary.html#term-distribution):
> A Python distribution is a versioned compressed archive file that contains Python packages, modules, and other resource files 

At the same time:
> ... it is not uncommon in Python to refer to a distribution using the term package

To complicate things even more, the glossary in [Python Package User Guide](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) has the term "Distribution Package" and contains the following remark:
> A distribution package is more commonly referred to with the single words "package" or "distribution"

Even in [PyPI documentation](https://pypi.org/help/#packages) we can read about _package_ term which essentialy is a _distribution_:
> A "file", also known as a "package", on PyPI is something that you can download and install. Because of different hardware, operating systems, and file formats, a release may have several files (packages), like an archive containing source code or a binary wheel.

Also Python libraries usually use services like GitHub or GitLab to store the code, so in fact repository name can be different from the distribution name.

As a result:
- We have two terms (_distribution_ and _package_) that have different meaning but sometimes are used interchangeably and therefore can confuse beginners
- The names for installation (_distribution_), working with code (_repository_) and import (_package_) can be different 

It was also complicated for me at the beginning, and I suppose that you start to clearly distinguish between these terms when you install libraries like _"scikit-learn"_ (distribution name) but import _"sklearn"_ (package name) or you delve into building Python libraries yourself.


## Naming
**Package**  
Python naming best practices for the packages (and modules) are clearly defined in [Style Guide for Python Code](https://peps.python.org/pep-0008/#package-and-module-names):  
> Modules should have short, all-lowercase names. Underscores can be used in the module name if it improves readability. Python packages should also have short, all-lowercase names, although the use of underscores is discouraged.

**Distribution**  
What about distribution names? To be honest, I didn't find any official information about it (e.g. in Python documentation or PEPs) and it is confirmed [here](https://stackoverflow.com/questions/54597212/using-hyphen-dash-in-python-repository-name-and-package-name/54599368#54599368):
> PEP 8 has nothing to do with the question as it doesn't talk about distribution, only about importable packages and modules.

This surprised me quite a lot because if I need to use underscores in the package name, what symbol I should use in distribution name?  
For example, I will choose the hyphens without hesitation. Why? I don't know :smile:  
Probably I've seen distribution names with hyphens much more often and this is the main reason for me to choose hyphen, but how to confirm it?  
Are there any compelling reasons to use hyphens?


## Research
To be more objective I decided to research it using different methods:
1. Search for the information on the internet
2. Gather distribution names statistics from PyPI repository
3. Ask people who have a lot of experience in building Python libraries

For the methods 2 and 3 you can find all the code and extended statistics in the [GitHub repository](https://github.com/labdmitriy/dist-analysis).

### Information from the internet
- Some answers from online resources contain the statements like [this](https://stackoverflow.com/a/11947816):
> 1. ... Long, concatenated words are hard to understand. ...
> 2. "_" is harder to type than "-"

- I've found 4 popular [cookiecutter](https://github.com/cookiecutter/cookiecutter) templates for Python packages:
  - [Cookiecutter Data Science](https://github.com/drivendata/cookiecutter-data-science)  
  [Version 2](https://github.com/drivendata/cookiecutter-data-science/tree/v2) of this template accepts hyphens for distribution names.
  - [Py-Pkgs](https://github.com/py-pkgs/py-pkgs-cookiecutter)  
    This template complements incredibly useful [book](https://py-pkgs.org/) and forces using underscores for distribution names.
  - [Hypermodern Python](https://github.com/cjolowicz/cookiecutter-hypermodern-python)  
  This template is based on awesome [article series](https://cjolowicz.github.io/posts/hypermodern-python-01-setup/) and there is an explicit [recommendation](https://cjolowicz.github.io/posts/hypermodern-python-01-setup/#:~:text=Use%20snake%20case%20for%20the%20package%20name%20hypermodern_python%2C%20as%20opposed%20to%20the%20kebab%20case%20used%20for%20the%20repository%20name%20hypermodern%2Dpython.%20In%20other%20words%2C%20name%20the%20package%20after%20your%20repository%2C%20replacing%20hyphens%20by%20underscores.) to use hyphen for distribution names.
  - [pylibrary](https://github.com/ionelmc/cookiecutter-pylibrary)  
    This template has explicit distinction between repository, distribution and package names and hyphens are converted to underscores for distribution names.


- There is also an interesting [research](https://moduscreate.com/blog/github-semantic-naming/) about repositories naming, but I was searching more facts about Python distribution itself based on more complete data.

### Naming statistics from PyPI repository
First of all we will get the list of all distributions and using the solution from [here](https://stackoverflow.com/questions/21419009/json-api-for-pypi-how-to-list-packages/30787985#30787985):
```python
distributions = get_distributions_list(base_url)
distributions_count = len(distributions)
print(distributions_count)

378373
```

<br>
Distribution names consist of uppercase and lowercase letters, digits and punctuation - hyphen, underscore and dot:
```python
name_chars = set(''.join(distributions))
print(''.join(sorted(name_chars)))

-.0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz
```

<br>
Let's calculate statistics for common char combinations which are interesting for us:
```python
no_char = set()
hyphen = set('-')
underscore = set('_')
dot = set('.')
punctuation = hyphen | underscore | dot
digits = set(string.digits)
letters = set(string.ascii_letters)
letters_digits = letters | digits


# Letters and digits
names_letters_digits = get_names_stats(distributions, letters_digits)

Possible chars: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
Required chars: 
Names count: 192944
Names proportion: 0.50973
Examples: ['harrpy', 'logconf', 'irate']


# With only hyphens
names_with_only_hyphens = get_names_stats(distributions, letters_digits | hyphen, 
                                          required_chars=hyphen)

Possible chars: -0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
Required chars: -
Names count: 161045
Names proportion: 0.42546
Examples: ['sentry-tablestore', 'requests-async-session', 'dash-loading-spinners']


# With only underscores
names_with_only_underscores = get_names_stats(distributions, letters_digits | underscore, 
                                              required_chars=underscore)

Possible chars: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz
Required chars: _
Names count: 10650
Names proportion: 0.02814
Examples: ['nextcloud_news_updater', 'nose_priority', 'dms_tools']


# With only dots
names_with_only_dots = get_names_stats(distributions, letters_digits | dot, 
                                       required_chars=dot)

Possible chars: .0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
Required chars: .
Names count: 11489
Names proportion: 0.03035
Examples: ['plone.introspector', 'RBX.py', 'danse.ins']


# With all punctuation
names_with_all_punctuation = get_names_stats(distributions, letters_digits | punctuation, 
                                             required_chars=punctuation)

Possible chars: -.0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz
Required chars: -._
Names count: 13
Names proportion: 0.00003
Examples: ['vision_utils-0.1.1', '0-._.-._.-._.-._.-._.-._.-0', 'sloth-ci.ext.docker_exec']
```
<br>
Additionaly I created visualization for names which contain one or multiple punctuation chars using [matplotlib-venn](https://github.com/konstantint/matplotlib-venn) library:
![Python distribution names with punctuation - Venn diagram](/assets/blog/2022-05-21-distributions-vs-packages/names_with_punctuation_venn.png)  
Due to the technical reasons there is a space for intersection of all 3 punctuation chars, but it is an expected behaviour and explained [here](https://github.com/konstantint/matplotlib-venn/issues/12).  

An alternative way which I found to visualize multiple sets relationship is [UpSetPlot](https://github.com/jnothman/UpSetPlot) and I decided to use it for our case:
![Python distribution names with punctuation - UpSetPlot](/assets/blog/2022-05-21-distributions-vs-packages/names_with_punctuation_upset.png)  

**Findinds**  
Based on these results we can make several interesting conclusions:
- There are more than half (~51%) of the distributions without any punctuation chars
- A lot of distributions use only hyphens in their names (~43%)
- Distribution names with only dots are also used (~3%) and more frequently than only underscores (~2.8%)
- There are distributions with all punctuation chars but there are very few of them (13 packages)  
  As we can see in the examples above, even distributions with names like `0-._.-._.-._.-._.-._.-._.-0` are acceptable for PyPI repository


### Answers from experts
I decided to ask the authors of `Py-Pkgs`, `pylibrary` and `Hypermodern Python` cookiecutter templates why they choose underscore or hyphen.

**Py-Pkgs**  
Co-author of the `Py-Pkgs` template, [Tomas Beuzen](https://www.tomasbeuzen.com/), [answers](https://github.com/py-pkgs/py-pkgs-cookiecutter/issues/48) about their choice:  
> ... because it is simpler, less cognitive-load (esp. for the beginner), and to us, it seems logical.`

Tomas also provides an interesting [link](https://developers.google.com/style/filenames) that Google now explicitly recommends using hyphens in file and directory names in their developer documentation (but they don't explain why).

**pylibrary**  
Author of the `pylibrary` template, [Ionel Cristian Mărieș](https://blog.ionelmc.ro/), has the following reason to choose hyphen:
> ... you'll see dashes more often than underscores in urls in general. I haven't given it much thought, just went with what people usually have in urls (dashes instead of underscores) ...  

He also gave the examples when repository, distribution and package names may differ:
> I guess the main reason I have this dist/package/project name distinction is that I don't always have a consistent scheme for all my projects:
>
- Sometimes the dist name I want is already taken on PyPI.
- Django projects have a differently styled project name.

> About the dashes... it's just styling at this point. https://pypi.org/project/lazy-object-proxy/ is the same as https://pypi.org/project/lazy_object_proxy/

**Hypermodern Python**  
Author of the `Hypermoder Python` template, [Claudio Jolowicz](https://cjolowicz.github.io/), really surprised me with his comprehensive [answer](https://github.com/cjolowicz/cookiecutter-hypermodern-python/discussions/1200) which includes both technical and historical notes.

His personal choice is also based on convenience:
> Personally, I prefer package names with hyphens because I find them easier to read. 

> This is obviously a personal choice, and there are well-known projects using underscores in their names. To name one prominent example, the import sorter previously used by this template was [reorder_python_imports](https://github.com/asottile/reorder_python_imports). 

Historical review is very interesting:
> Hyphens have been a part of standard typography [since Gutenberg](https://en.wikipedia.org/wiki/Hyphen#Origin_and_history).

> By contrast, we've had the underscore character only since the advent of typewriters. According to [this thesis](https://dash.harvard.edu/bitstream/handle/1/33797379/ROMANO-DOCUMENT-2016.pdf), it was a fixture of the keyboard by 1881, and used for underlining by backing up the carriage and typing over the previous letter. It became a part of ASCII in 1963, and the C programming language made it a legal character in identifiers in the 1970s. I think you'd be hard-pressed to find a text with underscores that's not aimed at technical people. What's more, the original purpose of underscores was to provide underlining for devices that support inserting multiple characters in the same position. I still find it somewhat weird to read identifiers that underline the gaps between words, but not the words themselves.

Also technical details are very useful:
> Using underscores for both distribution and import names has the advantage of consistency, as hyphens are not legal characters in Python identifiers, while underscores are.
>
> Worth noting that [PEP 503](https://peps.python.org/pep-0503/#normalized-names) treats the three non-alphanumeric characters (., -, _) in package names as equivalent to -. So the above example appears as [reorder-python-imports](https://pypi.org/project/reorder-python-imports/) on PyPI.

> PyPI preserves the original name in the package metadata, as specified by the package author. The URL uses the normalized name ([PEP 503](https://peps.python.org/pep-0503/#normalized-names)), but name variants are accepted and redirected to the normalized name.  
>
>PEP 503 normalization also includes transforming to lowercase. For example, the metadata name for pyyaml is `PyYAML`, whose canonical form is `pyyaml`.  
>
>There is also the question of normalizing distribution filenames. For wheels, this is specified in [PEP 427](https://peps.python.org/pep-0427/#escaping-and-unicode) and based on underscores and original case (for pyyaml, the wheel filename uses `PyYAML`). For sdists, this is unspecified (see [this blog](https://blog.yossarian.net/2022/05/09/A-most-vexing-parse-but-for-Python-packaging) and [this issue](https://github.com/pypa/packaging/issues/527)).  
>
>Tools like pip or Poetry will work with any name variant. For example, Poetry uses the original name from the package metadata (`PyYAML`) in pyproject.toml and poetry.lock, but you can `poetry add pyyaml`.

>As an aside, name variants also exist for the console script (where applicable).
- cogapp (cog)
- rst-to-myst (rst2myst)
Sometimes, only the "marketing name" and/or the repository name are different:
- coverage (distribution, package, and script are named coverage, repository is named coveragepy, human-friendly name is Coverage.py)

I am grateful to the authors of these templates for their answers and insights.  


## Additional experiments
**URL normalization**  
I was interested in trying URL normalization which was mentioned by experts and made a small experiment - use [python_reorder_import](https://github.com/asottile/reorder_python_imports/) package and watch how changing PyPI URL to the package will be processed:
```python
check_alternative_urls(distribution_name)

https://pypi.python.org/project/reorder-python_imports -> https://pypi.org/project/reorder-python-imports/
https://pypi.python.org/project/reorder.python_imports -> https://pypi.org/project/reorder-python-imports/
https://pypi.python.org/project/reorder_python-imports -> https://pypi.org/project/reorder-python-imports/
https://pypi.python.org/project/reorder_python.imports -> https://pypi.org/project/reorder-python-imports/
```
And all URLs are redirected to the URL with normalized distribution name as expected.

**Installation**  
An interesting fact that you can install distribution replacing any punctuation char by any number of hyphens, underscores and dots or change case of the letters, as mentioned in [Python Packaging User Guide](https://packaging.python.org/en/latest/specifications/core-metadata/?highlight=hyphen#name).
For example, the following commands will successfully install `python-reorder-import` distribution:
```python
pip install reorder-.__.-pYtHoN_-..-_imports
poetry add reorder-.__.-pYtHoN_-..-_imports
```

For uninstallation it is a little more complicated:
- For pip you can uninstall any alternative name:
```python
pip uninstall reorder---pYtHoN...imports
```
- It seems that for poetry you can use only normalized name:
```python
poetry remove reorder-python-imports
```


## Open questions
What I still don't understand is not consistent behavior for different packages.  

For example, I found the latest by update time package with all punctuation chars and now this package is [carson-tool.create_template](https://github.com/CarsonSlovoka/carson-tool.create_template).
Let's make the same experiments and compare results.

**URL normalization**  
```python
distribution_update_stats, incorrect_distributions = get_distribution_update_stats(base_url, names_with_all_punctuation)
distribution_stats = pd.Series(distribution_update_stats)
distribution_name = distribution_stats[distribution_stats == distribution_stats.max()].index.item()
distribution_info = get_distribution_info(base_url, distribution_name)['info']
distribution_url = distribution_info['project_url']
print(distribution_url)

'https://pypi.org/project/carson-tool.create_template/'
```
<br>
Here URL is redirected from multiple URL variants to the **original** one, not **normalized** version of the name:
```python
check_alternative_urls(distribution_name)

https://pypi.python.org/project/carson.tool.create_template -> https://pypi.org/project/carson-tool.create_template/
https://pypi.python.org/project/carson_tool.create_template -> https://pypi.org/project/carson-tool.create_template/
https://pypi.python.org/project/carson-tool-create_template -> https://pypi.org/project/carson-tool.create_template/
https://pypi.python.org/project/carson-tool_create_template -> https://pypi.org/project/carson-tool.create_template/
https://pypi.python.org/project/carson-tool.create-template -> https://pypi.org/project/carson-tool.create_template/
https://pypi.python.org/project/carson-tool.create.template -> https://pypi.org/project/carson-tool.create_template/
```
<br>
**Installation**  
If I install the package using `pip`:
```python
pip install carson-tool.create_template
```
<br>
Then I can see in the packages list that underscore char was replaced by hyphen but dot char remained unchanged:
```python
pip list

carson-tool.create-template 0.2.0
```
<br>
I would appreciate if somebody clarifies this behaviour because I didn't find any clear explanations for it.


## Summary  
- Terminology for distributions and packages in Python can be quite ambiguous and it takes practice in order to distinguish between them
- Packages naming has the most specific requirements
- Distributions naming is not so obvious though the choice can be supported by both historical and technical points of views