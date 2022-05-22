---
title: Latex
parent: Collections
---

# Table

## Bold `\hline`
```latex
\usepackage{makecell}

% inside table
\Xhline{1.5pt}
\Xhline{2\arrayrulewidth}
```

## Multiple rows and columns
```latex
%multi-column
\multicolumn{number cols}{align}{text} % align: l,c,r
 
%multi-row
\usepackage{multirow}
\multirow{number rows}{width}{text}
% Using * as width, the text argument’s natural width is used.
```
## Align cell in `tabularx`
```latex
{>{\raggedright\arraybackslash}X} % align left
{>{\centering\arraybackslash}X}   % align center
{>{\raggedleft\arraybackslash}X}  % align right
```
Difference between `c` and `>{\centering\arraybackslash}X`: The first do alignment w.r.t the local cells, while the latter w.r.t the global space. See below two examples:
```latex
\begin{tabularx}{\textwidth}{l l l l l c c c c c}
```
![Screenshot from 2022-05-22 20-23-53](https://user-images.githubusercontent.com/42603768/169694981-c1b90bc0-a35f-47d3-9c83-a2eba856d95e.png)

```latex
\begin{tabularx}{\textwidth}{l l l l l *{5}{>{\centering\arraybackslash}X}}
```
![Screenshot from 2022-05-22 20-25-57](https://user-images.githubusercontent.com/42603768/169695042-3d9d7722-cf40-44b3-99a0-99f3b010b0e1.png)

## Repeated alignments
```latex
*{num_repeated}{alignment} % see above example
```

## Custom alignment on specific cell
```latex
\multicolumn{1}{|r|}{Item3}
```
![Screenshot from 2022-05-22 20-33-43](https://user-images.githubusercontent.com/42603768/169695365-d016b983-283b-429b-beec-66437e45922f.png)
