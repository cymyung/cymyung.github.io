---
layout: single
title: Colab to Markdown
categories:
  - data_science
taxonomy: data_science
date: '2024-01-29'
published: True
---

Step 1: Download the .ipynb file
Click on File->Download and download the file as .ipynb file

Step 2: Upload the file again
Drag & Drop the downloaded notebook into the Files section of your Colab. This way, we make it available to the session storage.

Step 3: Convert it to Markdown
To convert the notebook to markdown, we use the nbconvert tool, which should already be installed in your Colab.

Add a new code cell in the top of your Colab and run this command:

!jupyter nbconvert --to markdown filename.ipynb

Now, the markdown file should appear in the Files section of the Colab as filename.md, and you can right-click on it (or click on the three dots next to it) and then download it.
