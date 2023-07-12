# mde-docs.github.io

MDE docs website uses [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/) to document all the pages. To install Material MkDocs do:

```pip install mkdocs-material```

Head over to the ``mde-docs-site`` (main directory) and start editing the webpages found in the ``docs`` folder. To live preview the edits do (in the main directory):

```mkdocs serve```

Then open a browser and go to ``localhost:8000`` to access the site. Just save any changes to a file to see the updated website.

IMPORTANT: ``assets/`` contains additional links which are relatively accessed from the markdown pages.

To build the website, once you've finished editing do:

```mkdocs build```
