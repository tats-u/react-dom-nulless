# `react-dom` without-NUL patch

This package is a patch for `react-dom` 18.3.1.

This packages is a backport of [facebook/react#26228](https://github.com/facebook/react/pull/26228), which fixes a bug that HTML generated by SSG or SSG can contain NUL especially in CJK languages, for React 18.x.

See the following issue in React and its frameworks for details:

- [facebook/react#24985](https://github.com/facebook/react/issues/24985)
- [facebook/react#31134](https://github.com/facebook/react/issues/31134)
- [facebook/docusaurus#9985](https://github.com/facebook/docusaurus/issues/9985)
- [meilisearch/docs-scraper#474](https://github.com/meilisearch/docs-scraper/issues/474)
- [remix-run/remix#8203](https://github.com/remix-run/remix/issues/8203)

## Usage

Update `react-dom` to 18.3.1 before the following steps.

### Patch

#### npm & Yarn v1

Use [`patch-package` package](https://github.com/ds300/patch-package).

After `patch-package` (and additional `postinstall-postinstall` only for Yarn) is installed and `"postinstall": "patch-package"` is added to your `package.json`, run the following command.

```sh
curl -LfO --output-dir node_modules/react-dom/cjs/react-dom-server.node.development.js https://github.com/tats-u/react-dom-no-nul/raw/850327cbc752bf95e94114699861d142741f4181/cjs/react-dom-server.node.development.js
curl -LfO --output-dir node_modules/react-dom/cjs/react-dom-server.node.production.min.js https://github.com/tats-u/react-dom-no-nul/raw/850327cbc752bf95e94114699861d142741f4181/cjs/react-dom-server.node.production.min.js
npx patch-package react-dom
```

Commit the generated `patches/react-dom+18.3.1.patch` file to your repository.

#### pnpm

Use the builtin `pnpm patch` command.

```sh
pnpm patch react-dom@18.3.1
# Copy the path of the generated temporary directory
# Assuming it is /path/to/temp
WORKSPACE_DIR=/path/to/temp
# PowerShell: $WORKSPACE_DIR = "/path/to/temp"
curl -LfO --output-dir "$WORKSPACE_DIR/cjs" https://github.com/tats-u/react-dom-no-nul/raw/850327cbc752bf95e94114699861d142741f4181/cjs/react-dom-server.node.development.js
curl -LfO --output-dir "$WORKSPACE_DIR/cjs" https://github.com/tats-u/react-dom-no-nul/raw/850327cbc752bf95e94114699861d142741f4181/cjs/react-dom-server.node.production.min.js
pnpm patch-commit "$WORKSPACE_DIR"
```

Commit the generated `patches/react-dom@18.3.1.patch` file and the modified `package.json` to your repository.

#### Yarn v2+

It supports the builtin `yarn patch` command like pnpm.

See [the Yarn documentation](https://yarnpkg.com/features/patching) for details.

### Install

`850327cbc752bf95e94114699861d142741f4181` is the commit ID of [the latest commit of branch `package-18.3.1`](https://github.com/tats-u/react-dom-no-nul/tree/package-18.3.1) and [the tag `patched-package-18.3.1`](https://github.com/tats-u/react-dom-no-nul/tree/patched-package-18.3.1). In case this repository were tampered by a cracker who had hijacked my account, please use the commit ID instead of the branch or the tag name.

```json
{
  "name": "your-react-app",
  "dependencies": {
    "some-react-framework": "^1.2.3",
    "other-dependencies": "^4.5.6",
    "react-dom": "github:tats-u/react-dom-no-nul#850327cbc752bf95e94114699861d142741f4181"
  }
}
```

Then, run `npm i` or `yarn` to install the package.

## When to use this patch

You can use this package **until you upgrade React to 19.x** if you're using React 18.x. That patch has been applied to React 19.x or newer. If you are going to upgrade React to 19.x or newer, you should replace this package with an official newer version by rewriting `dependencies` in `package.json`, or removing the patch created by `patch-package`, Yarn, or pnpm.

You do *not* need to use this package at all **if you're using Next.js** because it uses newer unstable versions of React.

You do *not* need to use this package at all if you don't use either of Node.js or npm packages. This bug can appear only when you use frameworks depending on the Node.js stream (and its polyfills in other JavaScript engines).

## Patch details


```sh
sed -i -e '11s/\(d<b.length&&(t(a,k\))/\1.subarray(0,l))/' node_modules/react-dom/cjs/react-dom-server.node.production.min.js
sed -i -e '129s/currentView/currentView.subarray(0, writtenBytes)/' node_modules/react-dom/cjs/react-dom-server.node.development.js
```

https://unpkg.com/browse/react-dom@18.3.1/cjs/react-dom-server.node.production.min.js
![image](https://github.com/user-attachments/assets/3d71dccf-0af5-49f3-b788-db4054a0da49)
Line 11, Character 243

Replace `t(a,k)` with `t(a,k).subarray(0,l)`

https://unpkg.com/browse/react-dom@18.3.1/cjs/react-dom-server.node.development.js
![image](https://github.com/user-attachments/assets/d19f6df9-4d58-4af4-9d6d-2c5844017273)
Line 129

Replace `currentView` with `currentView.subarray(0, writtenBytes)`

The other files are unchanged and equivalent to the original React 18.3.1 files.

## Patch diff

https://github.com/tats-u/react-dom-no-nul/commit/850327cbc752bf95e94114699861d142741f4181

[Diff in the original PR (facebook/react#26228)](https://github.com/facebook/react/pull/26228/files#diff-efdf85acf9fb91d560a8a49ac4f6480c2a4c59c4c67a8b3c0201083326495d7a) (Note: `(expression: any)` is the `any` casting expression in Flow, which is equivalent to `expression as any` in TypeScript)
