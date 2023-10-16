---
title: 'Deno First Impressions'
description: 'My first impressions of Deno'
pubDate: 2023-07-23
publish: true
---

I used [Deno](https://deno.com) for the first time this week.
I built a CLI using [Cliffy](https://deno.land/x/cliffy@v1.0.0-rc.3).

Overall first impressions were positive.
The [security model](https://deno.com/blog/a-more-secure-npm) is interesting:
basically Deno programs cannot access your filesystem, network, or environment unless explicitly granted at runtime with permission flags like `--allow-env`, `--allow-read`, etc.
If a program tries to access these protected resources, Deno displays a prompt:
```sh
┌ ⚠️  Deno requests read access to "/Users/bhb/.aws/credentials".
├ Requested by `Deno.readFile()` API.
├ Run again with --allow-read to bypass this prompt.
└ Allow? [y/n/A] (y = yes, allow; n = no, deny; A = allow all read permissions) >
```

Of course, all these measures can be subverted with the `--allow-all` flag, but at least the security-minded developer can audit untrusted code before granting permissions.

A few other Deno features stood out, especially compared to Node.js: the built-in [toolchain](https://docs.deno.com/runtime/manual/tools) and the extensive [standard library](https://deno.land/std).
Both remind me a lot of Go (in a positive way 😅).
It's great to start a project with zero dependencies and have all the boilerplate tooling (testing, formatting, linting, Typescript compiler) included.
Eliminating choices like which testing framework, which bundler, which package manager, how to configure typescript, etc. are a huge developer experience improvement.
This could also be framed as a security win: fewer dependencies means a smaller surface area for supply chain attacks.
In comparison, Node projects are notorious for dependency sprawl, which always worries me from a security standpoint.

## One roadblock...

Being able to use Node packages in Deno is a must, given the vast majority of the JS ecosystem is there.
Deno supports npm packages with [npm specifiers](https://docs.deno.com/runtime/manual/node/npm_specifiers). It even supports [custom registries](https://deno.com/blog/v1.29#custom-registry-support-via-environment-variable).

However, as of this writing, **Deno does NOT support different registries or credentials per package scope**.

Unfortunately, this would make Deno adoption a non-starter at my workplace.
We publish private packages to a private registry, and our projects consume these private packages alongside public ones from npm.
This is possible in Node because npm can be configured per package scope to use any registry:
```sh
# .npmrc
@myorg:registry=https://myorg-private-registry.com

//myorg-private-registry.com/:_authToken={AUTH_TOKEN}
```

Thus packages with the `@myorg` scope would use the private registry and supplied auth credentials, and all other packages would use the npm registry by default.

Right now this setup is not possible with Deno. Although it appears to be on their radar: https://github.com/denoland/deno/issues/16105

### A Workaround

Using a proxy registry can resolve this issue. I tried [Verdaccio](https://verdaccio.org/), with uplinks configured as follows, to access a private GitHub registry:

```yaml
uplinks:
  npmjs:
    url: https://registry.npmjs.org/
  github_private:
    url: https://npm.pkg.github.com/
    auth:
      type: bearer
      token_env: GITHUB_TOKEN
packages:
  '@myorg/*':
    access: $all
    publish: $authenticated
    unpublish: $authenticated
    proxy: github_private

  '**':
    access: $all
    publish: $authenticated
    unpublish: $authenticated
    proxy: npmjs
```

Run `verdaccio`, supplying any necessary auth tokens:
```sh
GITHUB_TOKEN=*** verdaccio
```

And set the Verdaccio server (localhost:4873 by default) as the NPM_CONFIG_REGISTRY for Deno:
```sh
NPM_CONFIG_REGISTRY=http://localhost:4873 deno cache ./mymodule.ts
```

## See also

Ryan Dahl, creator of Node.js, gave this talk at JSConf EU 2018:
[10 Things I Regret About Node.js](https://www.youtube.com/watch?v=M3BM9TB-8yA), which is basically his list of reasons for creating Deno.
