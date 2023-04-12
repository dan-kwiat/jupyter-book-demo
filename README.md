# Jupyter Book Demo

Jupyter Book is a Python package (`jupyter-book`) which helps turn a directory
of Jupyter notebooks into a website. See https://jupyterbook.org for more info.

## Anatomy of a Jupyter Book

A "Jupyter Book" also refers to your collection of files. Your directory should
contain:

- A configuration file `_config.yml`
- A table of contents file `_toc.yml`
- Your book's content (e.g. `.md` and `.ipynb` files)
- A `requirements.txt` file containing `jupyter-book`

See more here:
https://jupyterbook.org/en/stable/start/create.html#anatomy-of-a-book

## Building for the web

The `jupyter-book build` command spits out HTML to render your files in a web
browser, including a sidebar navigation of the pages and some other nice things.
It puts the HTML (and CSS, images etc) in a `_build/html` directory. There will
be a file called `index.html` which is the homepage.

The command can also build a PDF of your content.

## Deploying

Let's assume you have a repository where the `main` branch is just a Jupyter
Book i.e. a collection of files along with a YAML config and table of contents.

To deploy to a GitHub Pages site:

1. Create a new branch `gh-pages` on GitHub
2. Push the contents of `_build/html` to the `gh-pages` branch
3. From the repository on GitHub, click
   `Settings > Pages > Build and deployment` then select `Deploy from a branch`,
   choose `gh-pages` and save the changes.

> For free accounts, your repo needs to be public for GitHub Pages to work.

Now the domain `your-username.github.io/your-repo-name` will mirror the contents
of the `gh-pages` branch.

## Automating the build + deploy

Instead of manually building your book after each change and pushing to the
`gh-pages` branch, you can automate the whole process using
[GitHub Actions workflow](https://docs.github.com/en/actions/using-workflows).

To do this, simply add a `.github/workflows` directory to your `main` branch and
create a YAML file inside it. The name of this file doesn't matter, let's call
it `book.yml`. Paste the following contents into the file:

```yaml
# The name of this workflow
name: deploy-book

# Only run this when the main branch changes
on:
  push:
    branches:
      - main

# This job installs dependencies, build the book, and pushes it to `gh-pages`
jobs:
  deploy-book:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      # Install dependencies
      - name: Set up Python 3.7
        uses: actions/setup-python@v1
        with:
          python-version: 3.7

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
      # Build the book
      - name: Build the book
        run: |
          jupyter-book build .
      # Push the book's HTML to github-pages
      - name: GitHub Pages action
        uses: peaceiris/actions-gh-pages@v3.5.9
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./_build/html
```

One more thing: you must ensure the workflow has permission to update your
`gh-pages` branch. From the repository on GitHub, click on
`Settings > Actions > General > Workflow permissions` then select the option
`Read and write permissions` and save the changes.

_Remember to tell GitHub to deploy from this branch. See instructions above in
[Deploying](#deploying)_

That's it! Now any time you push changes to to the `main` branch, GitHub will
automatically convert your content into HTML, push the results to `gh-pages` and
deploy to `your-username.github.io/your-repo-name`.

## Demo

Note that this repo is a Jupyter Book with a GitHub Action workflow action. As a
result this README is deployed automatically to
https://dan-kwiat.github.io/jupyter-book-demo
