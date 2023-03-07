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

> Typeset Tabulars and Arrays with LATEX3.

- [Github repo](https://github.com/lvjr/tabularray)
- [Manual (2022A)](https://mirror.aarnet.edu.au/pub/CTAN/macros/latex/contrib/tabularray/tabularray.pdf)
- The one called in the Overleaf may not be the latest version, depending on the `texlive` version used.

# An example
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

# Mandatory argument
![image](https://user-images.githubusercontent.com/42603768/223297269-d980182d-62f6-4a2d-8112-3a9b50c4611a.png)

***BASIC Inner Specifications*** in mandatory argument (the column of new interfaces):

![image](https://user-images.githubusercontent.com/42603768/222946721-926ff5fb-95c1-488b-9c3d-6e8757f03779.png)

We categorize the basic keys into three classes: **Cells**, **Lines**, and **Rows and columns**, which are discussed in the next three sections, and then the last section we introduce the extra keys.

## The Old and New interfaces
The Old interfaces and New interfaces are two styles for the same function.

The Old interfaces (command-style):

![image](https://user-images.githubusercontent.com/42603768/222946481-825fd26b-b630-4ad4-afd3-aa3107341528.png)

The New interfaces (Innter specifications-sytle, **recommended**):

![image](https://user-images.githubusercontent.com/42603768/222946601-6e07be91-d0d4-4a77-978f-0b4123194290.png)

# About Rows and Columns
## `rows` and `columns`. 
`rows = {$KWARGS_OF_ROW}, colmuns = {$KWARGS_COLUMN}`

![image](https://user-images.githubusercontent.com/42603768/222944839-9933d1fc-3511-41b5-8acf-d05549829bea.png)

## `row` and `column`
`row{$ROW_INDEX} = {$KWARGS_OF_ROW}, column{$COLUMN_INDEX} = {$KWARGS_OF_COLUMN}`

![image](https://user-images.githubusercontent.com/42603768/222945209-1670ac55-ef9f-4cc8-8fd8-8bc913e39151.png)

You may select multiple rows/column indexes at once, e.g., `row{1,3,9}`, `row{3-6}`, `row{even}`, row{odd}.

## List of **keys** for `row` and `column`:

![image](https://user-images.githubusercontent.com/42603768/222945017-df9ecb46-e67d-4104-9f47-9fc584420660.png)![image](https://user-images.githubusercontent.com/42603768/222945033-03769634-b680-44ca-b47b-bee5d77cefb7.png)

You can find many examples demonstrating the usage of these keys in the manul.


## `colspec` and `rowspec`
`colspec={$LIST_OF_COLUMN_TYPES}, rowspec={$LIST_OF_ROW_TYPES}`

![image](https://user-images.githubusercontent.com/42603768/222945543-e0eaacb5-db4a-4757-8b97-49845c528475.png)

The `\begin{tblr}{|X|X|X|}` is equal to `\begin{tblr}{colspec={|X|X|X|}}`. The `colspec=` can be omitted if it is the only key inside the mandatory argument.
### Column types `X`
Here I only introduce the `X` column type. Please refer to the manul for more column types and row types.

`X[$WIDTH, $ALIGNMENT]`

![image](https://user-images.githubusercontent.com/42603768/222944575-ae19f292-dd42-488b-b62d-2760d1e920b4.png)

`$WIDTH=-1` means the width depends on the text length.

# About Cells
## `cells`
`cells = {$KWARGS_OF_CELL}`

![image](https://user-images.githubusercontent.com/42603768/223299304-27ed5383-587d-4acb-be43-d1ce6033a91b.png)

## `cell{i}{j}`
`cell{i}{j} = {$KWARGS_OF_MULTISPAN}{$KWARGS_OF_CELL}`, the `$KWARGS_OF_MULTISPAN` controls the multiple rows and columns and can be ommited.

![image](https://user-images.githubusercontent.com/42603768/223299749-83c75690-550e-408f-a59f-c115130539b4.png)

You may use the **OLD** interface `\SetCell` for building cells:
`\SetCell[$KWARGS_OF_MULTISPAN]{$KWARGS_OF_CELL} Cell content`

![image](https://user-images.githubusercontent.com/42603768/222944592-816619ab-694a-43ab-811e-3cb249418d32.png)

## List of **keys** for `cell`:

![image](https://user-images.githubusercontent.com/42603768/223301653-7013f193-1d7b-4888-be63-ead60731ca1c.png)
