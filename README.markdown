# Post-build Clean Buildpack

A simple buildpack to run after all other buildpacks have completed,
which removes a set of files defined in `.slug-post-clean`, so that they
are not included in the finished slug.

## Rationale

While this may seem to duplicate functionality provided by Heroku's
`.slugignore`, there is a key difference: `.slugignore`'d files are
removed after the repo is cloned, but before any buildpack is run. They
can therefore not be involved in the build process itself.

However, it is not uncommon for there to exist files in the repo that
are necessary for the build, but are not required at runtime. There may
also be installable build dependencies that are not runtime
dependencies.

In our case, a complex front-end build involves significant CSS, JS and
image assets, along with a large installation of node modules, all of
which are used only for building the production assets, but then remain
part of the slug.

## Usage

You can use [Heroku's multi-buildpack support](https://devcenter.heroku.com/articles/using-multiple-buildpacks-for-an-app)
to add  this buildpack to your existing buildpack(s). The post-build-clean buildpack **must** be last in the
buildpack order, which the following command will ensure:

```sh-session
heroku buildpacks:add wonderbly/post-build-clean
```

The `.slug-post-clean` file supports single-file and single-directory
declarations only, e.g.:

```
some_huge_file.psd
some/nested/directory
why_does_this_app_even_contain_a.tiff
```

I might expand it to support file globs, but for the moment it's not
necessary and the testing implications give me the willies.

## Using the latest source code

The `wonderbly/post-build-clean` buildpack from the [Heroku Buildpack Registry](https://devcenter.heroku.com/articles/buildpack-registry) represents the latest stable version of the buildpack. If you'd like to use the source code from this Github repository, you can set your buildpack to the Github URL:

```sh-session
heroku buildpacks:add https://github.com/Lostmyname/heroku-buildpack-post-build-clean.git
```
