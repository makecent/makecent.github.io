---
title: "Understanding the best table package for latex -- tabularray"
parent: Posts
---
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

## tabularray
> Typeset Tabulars and Arrays with LATEX3.

- [Github repo](https://github.com/lvjr/tabularray)
- [Manual (2022A)](https://ctan.math.illinois.edu/macros/latex/contrib/tabularray/tabularray.pdf)
- The one in the Overleaf may not be the latest version.

### An example
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

### Mandatory arguments
**Optional argumenst** of tblr environment are hardly used and details please refer to the manul.
Below table list the **basic** inner specifications (new interfaces):

![image](https://user-images.githubusercontent.com/42603768/222946721-926ff5fb-95c1-488b-9c3d-6e8757f03779.png)


### Multiple rows and columns
`\SetCell[r=$NUM_ROWS,c=$NUM_COLUMNS]{$ALIGNMENT} Text`

![image](https://user-images.githubusercontent.com/42603768/222944592-816619ab-694a-43ab-811e-3cb249418d32.png)

### Column `X`
`X[$WIDTH, $ALIGNMENT]`

![image](https://user-images.githubusercontent.com/42603768/222944575-ae19f292-dd42-488b-b62d-2760d1e920b4.png)

`$WIDTH=-1` means the width depends on the text length.

### Rows and Columns
`rows = {$KWARGS}, colmuns = {$KWARGS}`

![image](https://user-images.githubusercontent.com/42603768/222944839-9933d1fc-3511-41b5-8acf-d05549829bea.png)

The `columns = {15mm, c}` is equal to `columns = {15mm, halign=c}`

All **Keys** of rows and columns:

![image](https://user-images.githubusercontent.com/42603768/222945017-df9ecb46-e67d-4104-9f47-9fc584420660.png)
![image](https://user-images.githubusercontent.com/42603768/222945033-03769634-b680-44ca-b47b-bee5d77cefb7.png)

Some examples demonstrating the **Keys**:

![image](https://user-images.githubusercontent.com/42603768/222945283-4ee858f0-e521-414b-808d-b33cd241d17a.png)
![image](https://user-images.githubusercontent.com/42603768/222945291-ad0434c7-ef3d-42ca-801e-e44db7cc0786.png)


### Specific Row and Column
`row{$ROW_INDEX} = {$KWARGS}, column{$COLUMN_INDEX} = {$KWARGS}`

![image](https://user-images.githubusercontent.com/42603768/222945209-1670ac55-ef9f-4cc8-8fd8-8bc913e39151.png)

You may select multiple rows/column indexes at once, e.g., `column{1,3,4}={c}` set the alignment to "centering" for column 1,3, and 4.

### Colspec and Rowspec
`colspec={$LIST_OF_COLUMN_TYPES}, rowspec={$LIST_OF_ROW_TYPES}`

![image](https://user-images.githubusercontent.com/42603768/222945543-e0eaacb5-db4a-4757-8b97-49845c528475.png)

The `\begin{tblr}{|X|X|X|}` is equal to `\begin{tblr}{colspec={|X|X|X|}}`. The `colspec=` can be omitted if it is the only key inside the mandatory argument.

### The Old and New interfaces
The Old interfaces and New interfaces are two styles for the same function.

The Old interfaces (command-style):

![image](https://user-images.githubusercontent.com/42603768/222946481-825fd26b-b630-4ad4-afd3-aa3107341528.png)

The New interfaces (Innter specifications-sytle, **recommended**):

![image](https://user-images.githubusercontent.com/42603768/222946601-6e07be91-d0d4-4a77-978f-0b4123194290.png)
