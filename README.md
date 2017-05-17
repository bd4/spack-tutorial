# A Practical Introduction to Spack (for developers)

## Install from github

1. Fork on github

[LLNL/spack](https://github.com/LLNL/spack)

2. Clone your new fork

Example:
```
cd ~/codar
git clone git@github.com:bd4/spack.git
cd spack
```

3. Install

Start with the [official docs](http://spack.readthedocs.io/en/latest/getting_started.html#installation)

Spack is useless without Environment Modules or lmod, which is not immediately
clear from the docs.
Install using Linux distribution package manager or follow instructions
[here](http://spack.readthedocs.io/en/latest/getting_started.html#installenvironmentmodules). The shell integration step is recommended.

For OS X, see
[this guide](https://github.com/hpcugent/easybuild/blob/master/docs/Installing-environment-modules-without-root-permissions.rst) from easybuild.

4. Add remote for upstream LLNL repository

```
git remote add upstream git@github.com:LLNL/spack.git
```

Do this periodically to update your fork:

## Create a new package

1. Create a development branch

Make sure you have the latest from upstream before starting:
```
git checkout develop
git fetch upstream
git merge --ff-only upstream/develop
git push
```

Now actually create the branch (checks it out at the same time):
```
git checkout -b mypackage
```

Note: if a lot of time has passed between when you created the package and when
you are ready to submit a pull request, consider doing a rebase. As
long as no one else has updated your package, this can be done cleanly:
```
git checkout mypackage
git fetch upstream
git rebase upstream/develop
```

2. Follow the tutorial

The [official tutorial](http://spack.readthedocs.io/en/latest/tutorial_packaging.html) is well done. In many cases spack can detect the build system used and
generate the package automatically.

Note that spack requires a public available source tarball.

Example (SZ):
```
spack create -f http://www.mcs.anl.gov/~shdi/download/sz-1.4.9-beta.tar.gz
```

If spack doesn't detect your exotic  build system, try greping the repository
for other packages that use the same system and see what they do:
```
cd var/spack/repos/builtin/packages
grep -r scons
```

3. Commit

```
git add var/spack/repos/builtin/packages/mypackage.py
git commit
```

4. Add dependencies

Use `depends_on` function.

5. Test basic installation

```
spack install mypackage
```

6. Extra features: variants (greping for fun and profit)

See [python variant example](https://github.com/bd4/spack/commit/f53ff116f392252030e55b214a0856b2bc506410)

## Advanced Usage

### Installing unpublished versions

You can create a local mirror directory that contains tarballs for unreleased
versions during development. Create `~/.spack/mirrors.yaml`:
```
mirrors:
  manual: file:///home/bda/spack_mirror
```

Then put package tarball in
`~/spack_mirror/PACKAGENAME/PACKAGENAME-VERSION.tar.gz`.

I recommend using a letter based string for the version

### Install multiple versions of a package depending on different versions of dependency

The `^` character can be used to chain specifications for dependencies of
a package. For example, to build turbine against python 3.4 and load it into
the evironment:

```
spack install turbine+python^python@3.4
spack load turbine+python^python@3.4
```
