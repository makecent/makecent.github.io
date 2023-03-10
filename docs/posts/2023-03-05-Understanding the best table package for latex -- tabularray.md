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
`rows = {<kwargs_of_row>}, colmuns = {<kwargs_of_column>}`

![image](https://user-images.githubusercontent.com/42603768/222944839-9933d1fc-3511-41b5-8acf-d05549829bea.png)

## `row` and `column`
`row{<row_index>} = {<kwargs_of_row>}, column{<column_index>} = {<kwargs_of_column>}`

![image](https://user-images.githubusercontent.com/42603768/222945209-1670ac55-ef9f-4cc8-8fd8-8bc913e39151.png)

You may select multiple rows/column indexes at once, e.g., `row{1,3,9}`, `row{3-6}`, `row{even}`, row{odd}.

## List of **keys** (kwargs) for `row` and `column`:

![image](https://user-images.githubusercontent.com/42603768/222945017-df9ecb46-e67d-4104-9f47-9fc584420660.png)![image](https://user-images.githubusercontent.com/42603768/222945033-03769634-b680-44ca-b47b-bee5d77cefb7.png)

You can find many examples demonstrating the usage of these keys in the manul.


## `colspec` and `rowspec`
`colspec={<list_of_column_types>}, rowspec={<list_of_row_types>}`

![image](https://user-images.githubusercontent.com/42603768/222945543-e0eaacb5-db4a-4757-8b97-49845c528475.png)

The `\begin{tblr}{|X|X|X|}` is equal to `\begin{tblr}{colspec={|X|X|X|}}`. The `colspec=` can be omitted if it is the only key inside the mandatory argument.
### Column types `X`
Here I only introduce the `X` column type. Please refer to the manul for more column types and row types.

`X[<width>, <alignment>]`

![image](https://user-images.githubusercontent.com/42603768/222944575-ae19f292-dd42-488b-b62d-2760d1e920b4.png)

`<width>=-1` means the width depends on the text length.

# About Cells
## `cells`
`cells = {<kwargs_of_cell>}`

![image](https://user-images.githubusercontent.com/42603768/223299304-27ed5383-587d-4acb-be43-d1ce6033a91b.png)

## `cell{i}{j}`
`cell{i}{j} = {<kwargs_of_span>}{<kwargs_of_cell>}`, the `<kwargs_of_span>` is about the multiple rows and columns and can be ommited.

![image](https://user-images.githubusercontent.com/42603768/223299749-83c75690-550e-408f-a59f-c115130539b4.png)

You may use the **OLD** interface `\SetCell` for building cells:
`\SetCell[<kwargs_of_span>]{<kwargs_of_cell>} Cell content` (note the difference between the `[]` and `{}`)

![image](https://user-images.githubusercontent.com/42603768/222944592-816619ab-694a-43ab-811e-3cb249418d32.png)

## List of **keys** for `cell`:

![image](https://user-images.githubusercontent.com/42603768/223303204-80b1d82a-9837-400b-b243-1ea1a9cbb409.png)

# About Lines
## `hlines` and `vlines`
`hlines = {<index_of_line>}{<index_of_column>}{<kwargs_of_line>}`, where the `<index_of_line>` is normally omitted and only specified when multiple adjacent lines is needed, the `<index_of_column>` can be omitted and its default values `{-}` means drawing hlines across all columns, the `<kwargs_of_line>` can also be omitted meaning the default line format. `vlines` is the same with `hlines` but specify the index of rows to for the vertical lines to cross.

![image](https://user-images.githubusercontent.com/42603768/224300688-494d8a43-2c01-4a05-8292-0312fa6bf7a1.png)
![image](https://user-images.githubusercontent.com/42603768/224301712-00b7805a-e2d8-46cf-a941-9a835804ac83.png)

## `hline` and `vlines`
`hline{<index_of_row>} = <index_of_line>}{<index_of_column>}{<kwargs_of_line>}`, share the same arguments with `hlines` but can specify which row by `<index_of_row>` to draw horizontal lines, vice versa for the `vline`.
![image](https://user-images.githubusercontent.com/42603768/224303439-935d098a-f53f-41b2-af1d-8e02fef80fdc.png)

Similar to the `cell`, you may use the **OLD** interfaces `\SetHline`,`\hline`, and `\cline` for building horizontal lines:
`\SetHline = [<index_of_line>]{<index_of_column>}{<kwargs_of_line>}`
`\hline = [<kwargs_of_line>]`
`\cline = [<kwargs_of_line>]{<index_of_column>}`

![image](https://user-images.githubusercontent.com/42603768/224303989-ceb8da28-9b95-4285-b687-0deb7ec7b5b1.png)

The usage of `SetVline`, `\vline`, and `\rline` is similar to the above ones.

## List of **keys** for `hline` and `vline`:
![image](https://user-images.githubusercontent.com/42603768/224307452-8bdaf378-3729-4c6d-994c-5e6a345d53fd.png)



# Miscellaneous
## Using `talltblr` in the `table` environment
`talltblr` is designed to contain `caption`, therefore directly putting `talltblr` inside the `table` environment will cause two captions:
```latex
\renewcommand\TblrOverlap[1]{#1}

\begin{document}
\begin{table*}[!tb]
\caption{Comparison with State-of-the-Art Methods on THUMOS14.}
\label{T14_compare}
\centering
\begin{talltblr}[
caption = {Some introduction. $\dag$: the model with focal loss},
note{$\dag$} = {the model with focal loss},
]{
colspec = {c X},
hlines,
vlines,
}
G-TAD$^\dag$ [1] & Foo \\
BMN\TblrNote{$\dag$} [2] & Bar\\
\end{talltblr}
\end{table*}
```
![image](https://user-images.githubusercontent.com/42603768/224278647-e04fce6c-bbfe-4368-82b9-0f872adc9c07.png)

One simple solution is that we do NOT use it inside the `table` but a solely `talltblr`. While this may cause inconsistency if your other tables are of type `table`. For example, the distance between the caption and the table (headsep) and the size of caption maybe different for the `table` and `talltblr`.

Another tricky solution is to keep the `talltblr` inside the `table` but delete the caption of `talltable`:
```latex
\renewcommand\TblrOverlap[1]{#1}

\begin{document}
\begin{table*}[!tb]
\caption{Comparison with State-of-the-Art Methods on THUMOS14.}
\label{T14_compare}
\centering
%%%%%% ADDED %%%%%%%
\SetTblrTemplate{head}{empty}       %remove the header
%%%%%%%%%%%%%%%%%%%%
\begin{talltblr}[
caption = {Some introduction. $\dag$: the model with focal loss},
note{$\dag$} = {the model with focal loss},
]{
colspec = {c X},
hlines,
vlines,
}
G-TAD$^\dag$ [1] & Foo \\
BMN\TblrNote{$\dag$} [2] & Bar\\
\end{talltblr}
\end{table*}

\begin{table*}[!tb]
\caption{Comparison with State-of-the-Art Methods on THUMOS14.}
\label{T14_compare}
\begin{tabularx}{1\textwidth}{|c|X|}
\hline
G-TAD$^\dag$ [1] & Foo \\
\hline
BMN\TblrNote{$\dag$} [2] & Bar\\
\hline
\end{tabularx}
\end{table*}
```
![image](https://user-images.githubusercontent.com/42603768/224282094-ad7df008-5558-4254-8927-015845369e1b.png)
![image](https://user-images.githubusercontent.com/42603768/224282161-a9d30a32-a2fd-4d75-b926-f2f1aa8ae678.png)

Notes: Not applied to `longtblr` but only `talltblr`. If applying on the `longtblr`, the white space after detelting the header still remain.
