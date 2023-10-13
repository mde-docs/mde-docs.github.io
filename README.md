# MDE Docs

MDE docs website uses [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/) to document all the pages. To install Material MkDocs do:
`pip install mkdocs-material`

This project has been configured to use custom Pygments lexer plugins for [YAMTL](https://github.com/iNafey/yamtl-pygments-lexer) and [ETL](https://github.com/iNafey/pygments-epsilon). These have been installed using `pip` package installer in the `.github/workflows/ci.yml` file. Thus, you can use the syntax highlighting for all YAMTL variants and some of the Epsilon languages using their aliases (these can be found in their documentation linked above). If you want to download YAMTL Sytax Highlighter Python Package on your local machine then run:
`pip install yamtl-pygments-lexer`

This website also uses a manually installed plug-in for time stamping content. So please install the plug-in before use:
`pip install mkdocs-git-revision-date-localized-plugin`

**For WINDOWS USERS: Make sure to define the mkdocs path in the environment variable path as it is necessary.**

The ``./mde-docs.github.io`` folder is the **main directory** of this project. You can start editing the webpages found in the ``docs`` folder. To live preview the edits, use terminal to navigate to the main directory (this directory) and run:

`mkdocs serve`

Then open a browser and go to ``localhost:8000`` to access the site. Just save any changes to a file to see the updated website.

IMPORTANT: ``assets/`` contains additional links which are relatively accessed from the markdown pages.

To build the website, once you've finished editing do:

`mkdocs build`

## Project Structure

The project directory structure is as follows:

* Markdown files ending with `.md` inside the `docs/` folder are the main content of the site. These are the landing page (`index.md`) and the MTL related pages (`etl.md`, `yamtl.md` and `epsilon.md`). **Note:** documentation for MTL tools can be found in the `Model Transformation Languages` section of the live website.

* `docs/assets` folder contains the external files embedded into the site like images and download files.

* `docs/examples` folder has the problem description pages for each of the documented examples. You can find different MTL solutions to each case within the example pages.

* `docs/tutorials` folder includes the MTL solutions (either in ETL or YAMTL) for all example cases. Each solution is explained thoroughly and the project setup is clearly stated. **Note:** these pages are referred within the example pages (`docs/examples`) so that the examples are documented in a case-solution format.
