# Commit Hooks with SFDX

## Setup this demo

Pull down this repo and run `npm install` and that's it! Every commit you make will run the following commit hooks. You can copy the `package.json`, `cspell.json`, and the `.prettierrc` files in this repo as a quick a template to apply these commit hooks into your repo.

* Prettier - automatically formats your code before the commit
* cspell - checks the spelling in your code and throws an error if it finds any spelling mistakes

## Commit Hooks

When you create a new SFDX project in VS code, a `package.json` file is added to your project with some defaults that sets you up with commit hooks. If you don't, follow this blog to setup your `package.json` file to include husky, a tool needed to run commit hooks: https://developer.salesforce.com/blogs/2019/07/live-coding-continuous-integration-with-salesforce.html

### Setup `prettier` and `prettier-plugin-apex`

`prettier` and `prettier-plugin-apex` are two npm packages that are used to format your text and are included by default in your `package.json` dev dependencies. 
See the documentation at their respective pages:

* https://prettier.io/
* https://github.com/dangmai/prettier-plugin-apex

If they are not included, install them with the following:

```
npm install prettier --save-dev
npm install prettier-plugin-apex --save-dev
```

Next update the .prettierrc file with the following.

```
{
  "trailingComma": "none",
  "overrides": [
    {
      "files": "**/lwc/**/*.html",
      "options": { "parser": "lwc" }
    },
    {
      "files": "*.{cmp,page,component}",
      "options": { "parser": "html" }
    },
    {
      "files": "*.cls",
      "options": { 
        "parser": "apex",
        "plugins": ["./node_modules/prettier-plugin-apex"]
      }
    }
  ]
}
```

The key part that doesn't come included is the following block, which allows prettier to work with apex class files, so make sure to add it even if you already have `.prettierc` in your repo.

```
    {
      "files": "*.cls",
      "options": { 
        "parser": "apex",
        "plugins": ["./node_modules/prettier-plugin-apex"]
      }
    }
```

### Setup `cspell`

`cspell` is a package that does spell checking. Check out their git repo for advanced documentation: https://github.com/streetsidesoftware/cspell

Install with the following:


```
npm install cspell --save-dev
```

## Setup `husky`

The last thing we need to do is set up husky in `package.json` so that we run our pre-commit hooks. Make sure the following is included in your `package.json` file:

```
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "**/*.{cls,cmp,component,css,html,js,json,md,page,trigger,xml,yaml,yml}": [
      "prettier --write"
    ],
    "*.{cls,apex,js,html,md,xml,sql,py,yml,yaml}": [
      "cspell"
    ]
  }
```

Let's break this down. The following property is telling husky to run the `lint-staged` as a pre-commit hook.
```
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
```

The following property is defining `lint-staged`. Each property key in the `lint-staged` object specifies file name patterns, and the propety itself is an array of commands to run for those files. In this case, we specify files to run `prettier --write` on and a list of files to run `cspell` on.
```
  "lint-staged": {
    "**/*.{cls,cmp,component,css,html,js,json,md,page,trigger,xml,yaml,yml}": [
      "prettier --write"
    ],
    "*.{cls,apex,js,html,md,xml,sql,py,yml,yaml}": [
      "cspell"
    ]
  }
```

After you are done setting this all up you may need to run `npm rebuild`. I'm not sure if this is necessary, but I did need to do it to get my commit hooks to start running on each commit.
