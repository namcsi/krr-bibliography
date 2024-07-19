# Bibliography

> BibTeX bibliography files of all papers referenced by the group

## Installation

These instructions will make all BibTeX files in this repository available to BibTeX on your machine.

The following commands will first locate or create the directory where your TeX installation expects BibTeX files.
This repository will then be cloned to that location.

```sh
$ mkdir -p $(kpsewhich -var-value=TEXMFHOME)/bibtex/bib
$ cd "$_"
$ git clone https://github.com/krr-up/bibliography.git
```

If you prefer, you can clone the repository to another location and then create a symbolic link to the path TeX expects.

```sh
$ cd <preferred location>
$ git clone https://github.com/krr-up/bibliography.git
$ mkdir -p $(kpsewhich -var-value=TEXMFHOME)/bibtex/bib
$ ln -s <preferred location>/bibliography "$_"
```

Now, you can use the bibliography in your LaTeX file:

```latex
\bibliography{krr,procs}
```

## Contributing New BibTeX Entries

Before creating new BibTeX entries, **check whether they are already contained** in [krr.bib] or [procs.bib].
If not, add the new entries to [krr.bib] and **open a pull request.**
Please make sure that new BibTeX entries are **in line with the [guidelines](#guidelines-for-new-bibtex-entries)** below.
We will then review and merge your pull request in a timely fashion.

## Guidelines for New BibTeX Entries

### BibTeX Keys

BibTeX keys must **only contain ASCII characters.**
Depending on the number of authors, BibTeX keys should be chosen as follows:

- **1 author:** `[author][year][a-z]` (author’s *full* last name)

  > example: `lierler08a` for a 2008 paper by Y. Lierler
- **2 authors:** `[author 1][author 2][year][a-z]` (authors’ last names shortened to the *first 3 letters*)

  > example: `rantin06a` for a 2006 paper by S. Ranise and C. Tinelli
- **3+ authors:** `[author 1]...[author n][year][a-z]` (authors’ last names shortened to the *first 2 letters*)

  > example: `lirasm98a` for a 1998 paper by X. Liu and C. Ramakrishnan and S. Smolka

As the **`[year]`** field, use the *last 2 digits* of the publication year.

For the **`[a-z]`** suffix, `a` should be selected by default.
Should the resulting BibTeX key already exist, change the suffix to `b`, `c`, and so on until you obtain a **unique BibTeX key.**

For **proceedings** (mainly in [procs.bib]), use the following format instead:

- `[acronym][year]`

  > example: `lpnmr09` for *Proceedings of the Tenth International Conference on Logic Programming and Nonmonotonic Reasoning (LPNMR’09)*

### BibTeX Entries

Use `{` instead of `"` as the **field delimiter.**

> example:
> ```bibtex
> @inproceedings{marlyn07a,
>   author = {J. Marques-Silva and I. Lynce},
>   title = {Towards Robust {CNF} Encodings of Cardinality Constraints},
>   pages = {483-497},
>   crossref = {cp07}
> }
> ```

In the `author` and `editor` fields, **abbreviate first names** to the *first letter* and omit additional first names.
Spell out last names.

> example: `author = {M. Gebser and B. Kaufmann and T. Schaub}`

In the `title` field, **enclose capital letters** (other than the first letter) that must not be converted to lowercase letters by `{` and `}`.

> examples: `{SAT}`, `{P}etri Nets`

For proceedings with more than one reference, please use **`crossref`s**.

> example: `crossref = {aaai17}`

**Conference or workshop titles** should almost always start with “Proceedings of the …”

> example:
> ```bibtex
> @proceedings{aaai17,
>   editor = {P. Satinder and S. Markovitch},
>   title = {Proceedings of the Thirty-first National Conference on Artificial Intelligence (AAAI'17)},
>   booktitle = {Proceedings of the Thirty-first National Conference on Artificial Intelligence (AAAI'17)},
>   publisher = {AAAI Press},
>   year = {2017}
> }
> ```

Don’t abbreviate **journal names.**

> example: `ACM Transactions on Computational Logic` but not `ACM Trans. on Comp. Log.`

Use the **strings** defined in [krr.bib] for journal names.

> example: `@string{lncs = {Lecture Notes in Computer Science}}` for use in BibTex entries as `series = lncs`

Use LaTeX commands for **special characters** in all fields avoiding unnecessary braces but putting them around the command.

> example: `J. P{\"a}tynen` but neither `J. Pätynen`, `J. P\"atynen`, `J. P\"{a}tynen`, nor `J. P{\"{a}}tynen`.

Use `-` rather than `--` for **hyphens** in the pages, volume, and number fields.
Don’t terminate field contents with `.`.
The bibliography style and BibTeX will sort it out uniformly.

> example: `pages = {203-208}`

### Autoformatting

Always run the [bibfmt] script to format entries:
```sh
./bibfmt.py format
```

The script takes care of sorting, indenting, replacing unicode characters, and spacing.
Nevertheless, **always** check the result of the script.
Especially, when pasting contents from external sources (like titles of PDFs)
make sure that special characters like ligatures were replaced correctly.

The script requires at least Python 3.7 and the [bibtexparser] 1.3 module.
It is quite easy to setup with anaconda:
```sh
# install anaconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
# I recommend not to let the installer modify the bashrc but rather add a line
# like this manually:
#   source <install-prefix>/conda/etc/profile.d/conda.sh

# setup environment
conda create -n bib python=3.8 pip
conda activate bib
pip install bibtexparser

# run bibfmt
conda activate bib
python bibfmt.py format
```

### Amade's setup

This fork of the krr bibliography includes some changes to make the
bibliography setup compatible with my bibliography management system
in Emacs, based on the [[https://github.com/emacs-citar/citar][citar]]
package. 

Because the parser citar uses expects references to cross references
and string definitions to be within the same bib file, I create the
complete.bib file, which is a concatenation of procs.bib and
krr.bib. We do not keep track of complete.bib in git as we consider it
an artifact of krr.bib and procs.bib Two way syncing between
complete.bib and procs.bib + krr.bib is handled by some custom
interactive elisp functions `na/update-complete-bib` and
`na/update-krr-procs-bib`.

My workflow for maintaining this fork and contributing back is defined
below:

#### Pulling in changes from krr-up

Additional changes made, such as adding additional entries etc. in the fork
should be rebased on top of the master branch of
krr-up/bibliograpy. To this end, `branch.amade.rebase` is set to
`true` in the local git config.

After such a pull happens, complete.bib should be updated with the new
changes by calling the elisp function `na/update-complete-bib`.

#### Adding new commits and contributing back to krr-up

When adding new entries, there should be a separate commit for each
new entry in krr.bib and/or procs.bib.

If we want to contribute back an entry, we can then first need to
interactively rebase the associated commit to be just after the tip of
krr-up/master. We then create a new branch at the commit by using the
command `git branch branch_name <commit-hash>`, or by putting point on
the desired ref and pressing `b n`. After pushing, the pull request
can be opened from this branch.

[krr.bib]: krr.bib
[procs.bib]: procs.bib
[bibfmt]: bibfmt.py
[bibtexparser]: https://github.com/sciunto-org/python-bibtexparser
