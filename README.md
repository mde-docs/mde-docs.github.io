# mde-docs.github.io

MDE docs website uses [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/) to document all the pages. To install Material MkDocs do:

```pip install mkdocs-material```

**For WINDOWS USERS: Make sure to define the mkdocs path in the environment variable path as it is necessary.**

Head over to the ``mde-docs-site`` folder which is the **main directory** of this project. You can start editing the webpages found in the ``docs`` folder. To live preview the edits, use terminal to navigate to the project's main directory and run:

```mkdocs serve```

Then open a browser and go to ``localhost:8000`` to access the site. Just save any changes to a file to see the updated website.

IMPORTANT: ``assets/`` contains additional links which are relatively accessed from the markdown pages.

To build the website, once you've finished editing do:

```mkdocs build```
