import subprocess as sp
import os

def call(cmd):
    print " ".join(cmd)
    output = sp.check_output(cmd)
    return output


def make_gh_pages(target, source, env):
    target_name, = map(lambda x: x.path, target)
    sources_names = map(lambda x: x.path, source)

    # checkout the gh-pages branch, and pull in the relevant files
    # from master (e.g. images, stylesheets, etc.)
    call(["git", "checkout", "gh-pages"])
    call(["git", "checkout", "master", "--"] + sources_names[1:])

    # copy the slides to index.html and update them in the index
    call(["cp", sources_names[0], target_name])
    call(["git", "add", "-A", target_name])
    # update the other files as well
    call(["git", "add", "-A"] + sources_names[1:])

    # commit the changes, and push them to github if it's not a dryrun
    commit = call(["git", "log", "master", "-1", "--pretty=oneline", "--abbrev-commit"]).strip()
    call(["git", "commit", "-m", '"Generated gh-pages for {}"'.format(commit)])
    call(["git", "push", "origin", "gh-pages"])

    # checkout the master branch again
    call(["git", "checkout", "master"])


## Create the environment
env = Environment(ENV=os.environ)


## Convert example assignment notebook to HTML
env.Command(
    "example/Assignment.html",
    ["example/Assignment.ipynb"],
    " ".join([
        "ipython nbconvert",
        "--to html",
        "--FilesWriter.build_directory=example",
        "$SOURCE"]))

env.Command(
    "perceptron/Perceptron Demo.html",
    ["perceptron/Perceptron Demo.ipynb"],
    " ".join([
        "ipython nbconvert",
        "--to html",
        "--FilesWriter.build_directory=perceptron",
        "$SOURCE"]))

## Specify nbconvert target, to generate the slides
env.Command(
    "codeneuro-sf-2015.slides.html",
    ["codeneuro-sf-2015.ipynb", "reveal.tpl", "reveal.js", "example/Assignment.html", "perceptron/Perceptron Demo.html"],
    " ".join([
        "ipython nbconvert",
        "--RevealHelpTransformer.url_prefix=reveal.js",
        "--to slides",
        "--template reveal.tpl",
        "$SOURCE"]))

env.Alias("slides", "codeneuro-sf-2015.slides.html")


## Specify the gh-pages target, to generate the version of the slides
## for github pages
env.Command(
    "index.html",
    ["codeneuro-sf-2015.slides.html", "images", "custom.css", "reveal.js", "example/Assignment.html"],
    make_gh_pages)

env.Alias("gh-pages", "index.html")
