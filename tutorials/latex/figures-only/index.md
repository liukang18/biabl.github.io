---
layout: tutorials
title: Document with Only Figures
author: naomi
comments: true
date: 2017-02-23
---

## Objectives

After you complete this section, you should be able to:

1. Create a LaTex Document
2. Add figures to document with figure caption
3. Export document to PDF

## New LaTeX Document

For these tutorials I will be using [ShareLatex](http://www.sharelatex.com), a free online LaTeX editor.

The first thing you'll want to do is to create a *new blank document*. To start a new project from scratch, in the main page click the New Project button, you will see the next drop-down menu.

<img class="img-responsive" alt="" src="images/TheFirstDocumentEx4.png">

Then click Blank Project. A box will open where you should enter the name of your new project, then click Create.

<img class="img-responsive" alt="" src="images/TheFirstDocumentEx5.png">

After that you will be redirected to the editor.

## Upload Figures

To upload an image, in the editor go to the upper left corner and click the upload icon. Next, a dialogue box will pop up for you to upload your files.

<img class="img-responsive" alt="" src="images/UploadingImagesEx2.png">

There you can either drag and drop your files or click Select files(s) to open a file browser. Navigate to the right folder and select the images to upload. You can upload several files at once. After the upload process is complete you will see the files in the left panel.

## Edit Text

<img class="img-responsive" alt="" src="images/blank.png">

There some text preloaded, but delete the text and use the following code to generate the basic structure of your document.

``` latex
\documentclass[doc]{apa6}
\usepackage{hyperref}
\usepackage[american]{babel}
\usepackage[style=apa,sortcites=true,sorting=nyt]{biblatex}
\DeclareLanguageMapping{american}{american-apa}

\title{Decreased White Matter Integrity in Patients with Alzheimer's Disease}
\shorttitle{TBSS}
\author{Naomi J. Goodrich-Hunsaker}
\affiliation{Brigham Young University}

\begin{document}

\maketitle

%% Figure 1
\begin{figure*}
    \centering
        \includegraphics[width=\textwidth]{MD-AD-HC.jpg}
        \caption{Provide figure description here.}
        \label{fig:Figure1}
\end{figure*}

%% Figure 2
\begin{figure*}
    \centering
        \includegraphics[width=\textwidth]{FA-HC-AD.jpg}
        \caption{Provide figure description here.}
        \label{fig:Figure2}
\end{figure*}

%% Figure 3
\begin{figure*}
    \centering
        \includegraphics[width=\textwidth]{AD-AD-HC.jpg}
        \caption{Provide figure description here.}
        \label{fig:Figure3}
\end{figure*}

%% Figure 4
\begin{figure*}
    \centering
        \includegraphics[width=\textwidth]{RD-AD-HC.jpg}
        \caption{Provide figure description here.}
        \label{fig:Figure4}
\end{figure*}

\end{document}
```

Before you compile your LaTeX code, you will need to change the `\author` information, obviously, but also the name of your figures you've uploaded. The text for identifying your figures is as follows:

```latex
\includegraphics[width=\textwidth]{MD-AD-HC.jpg}
```

The file name `MD-AD-HC.jpg` is unique to my system. Depending on how you labeled your figure image, change *MD-AD-HC.jpg* accordingly.

To change the figure caption, edit the text found within these curly brackets:

```latex
\caption{Provide figure description here.}
```

## Export to PDF

To download your final PDF file, in the editor click the Menu icon in the upper left corner. Then in the download area click the PDF icon. After that and depending on your web browser, a window will pop up to open the file, save it to your Downloads folder or select a different location for the downloaded file. Unless you enter a different name, the downloaded PDF will have the same name as your project.
