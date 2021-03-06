# Pursuit

[![Build Status](https://api.travis-ci.org/purescript/pursuit.svg?branch=master)](http://travis-ci.org/purescript/pursuit)

Pursuit is a web application which hosts documentation for PureScript packages,
and lets you search for code by names or types, via Hoogle.

Pursuit is currently deployed at <http://pursuit.purescript.org>.

Information for package authors can be found at
<http://pursuit.purescript.org/help>.

## Development

It's recommended to use `stack`: <http://docs.haskellstack.org>.

To build in development mode:

```
$ stack build
```

To run the server:

```
$ stack exec pursuit
```

To build in production mode:

```
$ stack build --flag pursuit:-dev
```

## Database structure

Pursuit currently uses the filesystem as a database, since it requires no setup
and it makes it easy to use Git and GitHub for backing up. The data directory
is set via an environment variable (see [Configuration](#configuration)).

The structure is as follows:

```
/
  cache/
    packages/
      purescript-prelude/
        0.1.0/
          index.html
          docs/
            Prelude/
              index.html
  verified/
    purescript-prelude/
      0.1.0.json
      0.1.1.json
```

The `cache/` directory has files that mirror the URL structure of the web
application, and contains files which do not change and may be served as-is
without forwarding the request on to the Yesod application. See Handler.Caching
for more details.

The `verified/` directory stores uploaded packages.  Each package has its own
directory, and then there is a JSON file for each version. These JSON files
each contain a serialized `Package GithubUser`; see
Language.PureScript.Docs.Types in the compiler for details about these types.

The backup process simply involves rsyncing everything in the `verified/`
directory into a git repository, making a commit, and pushing it to GitHub.

## Configuration

All configuration is done at startup via environment variables. The relevant
code is in the Settings module.

All configuration variable names start with `PURSUIT_` (eg,
`PURSUIT_STATIC_DIR`). Most environment variables are not required, and have
sensible defaults if not specified. The ones which _are_ required are:

* `PURSUIT_GITHUB_CLIENT_ID`: Github OAuth client id, for signing users in.
* `PURSUIT_GITHUB_CLIENT_SECRET`: Github OAuth client secret, for signing users
  in.

See `src/Settings.hs` for more details.

One way to supply the application with environment variables (if you are on a
system which uses Bash) is to use a script like the one in
`config/development.env`.
