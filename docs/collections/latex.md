---
title: Latex
parent: Collections
---
1. TOC
{:toc}

# About Table
Which package to use? Below is some package you must know:

- `tabular`: The basic one [not recommended].
- [`array`](https://ctan.org/pkg/array): A extension of `tabular`. Depended by most of the other table packages.
- [`tabularx`](https://ctan.org/pkg/tabularx): I believe it is the most widely used one. [recommended]
- [`booktabs`](https://ctan.org/pkg/booktabs): Professional looking. Specifically designed for tables without vertical lines.
- [`tabularray`](https://ctan.org/pkg/tabularray): A new LaTeX3 package using a new writting logic. Well maintained and powerful. [recommended].

## Miscellaneous

### Bold `\hline`
```latex
\usepackage{makecell}

% inside table
\Xhline{1.5pt}
\Xhline{2\arrayrulewidth}
```

### Multiple rows and columns
```latex
%multi-column
\multicolumn{number cols}{align}{text} % align: l,c,r
 
%multi-row
\usepackage{multirow}
\multirow{number rows}{width}{text}
% Using * as width, the text argument’s natural width is used.
```
### Align cell in `tabularx`
One important feature of the `tabularx` to the `tabular` is the column type `X`, which automatically expands in order to make the table as wide as specified, e.g., `\textwidth`.
Below are the examples:
```latex
\begin{tabularx}{0.5\textwidth}{|l|l|l|}
```
![1677658472924](https://user-images.githubusercontent.com/42603768/222082043-950b6dd3-e0dd-403d-b8a6-14c3b0e05b37.jpg)
```latex
\begin{tabularx}{0.5\textwidth}{|X|X|X|}
```
![image](https://user-images.githubusercontent.com/42603768/222082195-5a9d09c7-6364-4e48-86b9-519b2a4c1e7b.png)

However, it's quite tricky to do alignment in `X` columns:
```latex
{>{\raggedright\arraybackslash}X} % align left
{>{\centering\arraybackslash}X}   % align center
{>{\raggedleft\arraybackslash}X}  % align right
```
Note that the `X` column and the `c` `l` `r` columns can be used in the same table:
```latex
\begin{tabularx}{\textwidth}{l l l l l *{5}{>{\centering\arraybackslash}X}}
```
![Screenshot from 2022-05-22 20-25-57](https://user-images.githubusercontent.com/42603768/169695042-3d9d7722-cf40-44b3-99a0-99f3b010b0e1.png)

In the above example, only the width of the rightest five columns are auto-ajusted. 

### Repeated columns
```latex
*{num_repeated}{alignment} % see the above example
```

### Custom alignment on specific cell
```latex
\multicolumn{1}{|r|}{Item3}
```
![Screenshot from 2022-05-22 20-33-43](https://user-images.githubusercontent.com/42603768/169695365-d016b983-283b-429b-beec-66437e45922f.png)

## tabularray
> Typeset Tabulars and Arrays with LATEX3.

- [Github repo](https://github.com/lvjr/tabularray)
- [Manual (2022A)](https://ctan.math.illinois.edu/macros/latex/contrib/tabularray/tabularray.pdf)
- The one in the Overleaf may not be the latest version.

```latex
\documentclass[10pt,journal,compsoc]{IEEEtran}
\usepackage{tabularray}

\begin{table}[!t]
\caption{Comparison of Attention mechanisms}
\label{D70_compare}
\centering
\begin{tblr}{
colspec = {l c | X[c]},
hline{1, 6} = {1.5pt, solid},
hline{2} = {1-2}{1pt, dashed},
hline{3},
row{5} = {cyan8},
}
\SetCell[c=2]{c} two column & & \SetCell[r=2]{c} two rows &\\
attention & out. proj. &\\
space-time & sth1 & 90.3\\
space-time & sth2 & 70.6\\
space-time & sth3 & 90.3
\end{tblr}
\end{table}
```
![Screenshot from 2022-05-28 16-46-01](https://user-images.githubusercontent.com/42603768/170818166-1bde1476-01ce-4bae-8899-b648d0c1ca1f.png)

