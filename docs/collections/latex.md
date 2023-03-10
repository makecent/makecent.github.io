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
- [`tabularx`](https://ctan.org/pkg/tabularx): Introduce the useful `X` column. **Recommended**.
- [`booktabs`](https://ctan.org/pkg/booktabs): Professional looking. Specifically designed for tables without vertical lines.
- [`tabularray`](https://ctan.org/pkg/tabularray): A new LaTeX3 package using a new writting logic. Well maintained and powerful. **Recommended**. You may read my [post](https://chongkai.site/docs/posts/2023-03-05-Understanding%20the%20best%20table%20package%20for%20latex%20--%20tabularray/) about it.

## Miscellaneous
### `X` column in the `tabularx`
One important feature of the `tabularx` is the column type `X`, which automatically expands in order to make the table as wide as specified, e.g., `\textwidth`.
Below are the examples:
```latex
\begin{tabularx}{0.5\textwidth}{|l|l|l|}
```
![1677680391194](https://user-images.githubusercontent.com/42603768/222167268-5956338a-8bdc-4c0e-9122-08faa6e587e8.jpg)
```latex
\begin{tabularx}{0.5\textwidth}{|X|X|X|}
```
![1677680452440](https://user-images.githubusercontent.com/42603768/222167284-b4ae1b7e-258e-4e24-aa0e-6293bc0af135.jpg)

However, it's quite tricky to do **alignment in `X` columns**:
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

In the above example, only the width of the rightest five columns, i.e. the `X` columns, are auto-ajusted. 

**Altough the `X` column is good**, there is a drawback that all `X` columns in the same table share the same width. I think that ideal `X` columns should have width proportional to their text contained. Below is an example:
```latex
\begin{tabularx}{\textwidth}{|X| X| X| X| X| X |X| X| X| X| X|}
```
![image](https://user-images.githubusercontent.com/42603768/222942543-ca70c3a4-5198-453a-b983-225f66fbe6b5.png)

As we can see that all `X` column share the same width. A better width arrangement should be that the columns with long texts have larger width. There are two ways to do that referring to this [answer](https://tex.stackexchange.com/a/629602/208981):

- Using `\extracolsep{\fill}` inside `tabular*`.
- The `X[-1]` column in the `tabularray` package.



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

### Repeated columns
```latex
*{num_repeated}{alignment} % see the above example
```

### Unbreackable text `\mbox`
Before:

![image](https://user-images.githubusercontent.com/42603768/222937183-d20b3975-3ed4-494f-8c73-91edc0799b7c.png)

After:

```latex
\mbox{Input volume $I_{\tau}$} & Loc. &  Cls. & Detection\\ 
```
![image](https://user-images.githubusercontent.com/42603768/222937146-d63458a2-35d9-4ae8-a5f6-745bf4f96acd.png)


### Custom alignment on specific cell
```latex
\multicolumn{1}{|r|}{Item3}
```
![Screenshot from 2022-05-22 20-33-43](https://user-images.githubusercontent.com/42603768/169695365-d016b983-283b-429b-beec-66437e45922f.png)

## Superscript in table
Either using `Test$^\dag$` and expalining the superscript in the Table title, or using the `\TblrNote{$\dag$}` in the `tabularray` package and describing the superscripts in notes `note{$\dag$} = {text}` under the table.
