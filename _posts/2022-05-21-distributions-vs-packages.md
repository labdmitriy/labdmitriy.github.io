---
title: "Distributions vs packages in Python"
date: 2022-05-21 23:00:00 +0300
toc: true
categories:
- Blog
tags:
- Python
- Packaging
---

## Motivation
Now I am gathering information and planning a series of articles dedicated to Python packages development, and while preparing I encounter some interesting facts that I hadn't thought about before.  
One of them is the ambiguity in terminology for distributions and packages in Python and their naming.  
So I decided to share some thoughts about it.

 
## Terminology
If we try to search for _"package"_ definition, we can find [here](https://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/glossary.html#term-package) that among others it is:
> A directory containing an \_\_init\_\_.py file ..., and also usually containing modules (possibly along with other packages).

But probably this is not totally correct, because without \_\_init\_\_.py file it is still can be used as a namespace package, and there is a great [article](https://bastien-antoine.fr/2022/01/discovering-python-namespace-packages/) about that.

For _"distribution"_ definition, in the same glossary we can find useful [note](https://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/glossary.html#term-distribution) that it is:
> A Python distribution is a versioned compressed archive file that contains Python packages, modules, and other resource files 

At the same time:
> ... it is not uncommon in Python to refer to a distribution using the term package

To complicate things even more, the glossary in [Python Package User Guide](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) has the term "Distribution Package" and contains the following remark:
> A distribution package is more commonly referred to with the single words "package" or "distribution"

As a result we have two terms that have different meaning but sometimes can be used interchangeably and therefore can confuse beginners.  

It was also complicated for me at the beginning, and I think that you start to clearly distinguish these terms when you install libraries like _"scikit-learn"_ (distribution name) but import _"sklearn"_ (package name), or you delve into building Python libraries yourself.


## Naming
For Python packages (and modules) naming best practices it is pretty easy to find information in [Style Guide for Python Code](https://peps.python.org/pep-0008/#package-and-module-names):  
> Modules should have short, all-lowercase names. Underscores can be used in the module name if it improves readability. Python packages should also have short, all-lowercase names, although the use of underscores is discouraged.

What about distribution names? To be honest, I didn't find any official information about it (e.g., in Python documentation or PEPs) and it is confirmed [here](https://stackoverflow.com/questions/54597212/using-hyphen-dash-in-python-repository-name-and-package-name/54599368#54599368):
> PEP 8 has nothing to do with the question as it doesn't talk about distribution, only about importable packages and modules.

This surprised me quite a lot because if I need to use underscores in the package name, what symbol I should use in distribution name - underscores or hyphens?  
For example, I will choose the hyphens without hesitation. Why? I don't know :smile:  
Are there any compelling reasons to use hyphens?

All I found is the statements like [this](https://stackoverflow.com/questions/11947587/is-there-a-naming-convention-for-git-repositories):
> "_" is harder to type than "-"

Probably I've never seen distribution names with underscores and this is the main reason for me to choose hyphen, but how to confirm it at least statistically?

Usually Python libraries use services like GitHub or GitLab to store the code and there is an interesting [research](https://moduscreate.com/blog/github-semantic-naming/) about repositories naming, but I was searching more facts about Python distribution itself based on more complete data.

Finally, to be more objective, I decided to gather naming stats for all Python distributions in PyPI repository (using the solution from [here](https://stackoverflow.com/questions/21419009/json-api-for-pypi-how-to-list-packages/30787985#30787985)):
```python
from  xmlrpc.client import ServerProxy

client = ServerProxy('https://pypi.python.org/pypi')
packages = client.list_packages()
len(packages)
376904

names_with_only_hyphens = [name for name in packages if '-' in name and '_' not in name]
len(names_with_only_hyphens)
162053

names_with_only_underscores = [name for name in packages if '-' not in name and '_' in name]
len(names_with_only_underscores)
11021

names_with_hyphens_and_underscores = [name for name in packages if '-' in name and '_' in name]
len(names_with_hyphens_and_underscores)
173

names_without_hyphens_and_underscores = [name for name in packages if '-' not in name and '_' not in name]
len(names_without_hyphens_and_underscores)
203657
```

Based on these results we can make several interesting conclusions:
- There are more than half (~54%) of the distributions without any underscores and hyphens
- Surprisingly (at least for me) a lot of distributions use hyphens in their names (~43%)
- Only ~3% of the distributions use underscores
- There are even distributions with underscores and hyphens but there are very few of them (~0.04%)


## Conclusion
- Terminology for distributions and packages in Python can be quite ambiguous and it takes practice in order to distinguish between them
- Packages naming has the most specific requirements
- Distribution naming is still not obvious for me though the choice can be supported at least statistically.

I will be glad to hear feedback including additional reasons for the distributions naming.
