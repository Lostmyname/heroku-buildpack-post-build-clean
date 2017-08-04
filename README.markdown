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

You will need to use the [`heroku-buildpack-multi`
buildpack](https://github.com/ddollar/heroku-buildpack-multi), and add
your existing buildpack(s) to the `.buildpacks` file in your application
repository. The post-build-clean buildpack **must** be last in the
buildpack order:

```
# .buildpacks
https://github.com/heroku/heroku-buildpack-nodejs
https://github.com/heroku/heroku-buildpack-ruby
https://github.com/Lostmyname/heroku-buildpack-post-build-clean
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
