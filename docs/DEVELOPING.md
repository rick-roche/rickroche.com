# Developing in this repo
## Installation

Ensure that you have installed
- [Hugo](https://gohugo.io/getting-started/quick-start/#step-1-install-hugo)
- [Netlify Dev](https://www.netlify.com/products/dev/#how-it-works)

On macOS you can run

```sh
brew install hugo netlify-cli
```

## Adding content

To [add a new post](https://gohugo.io/getting-started/quick-start/#step-1-install-hugo)

```sh
hugo -s site/ new posts/$(date +%F)-post-name/index.md
```

To test locally run the following

```sh
git submodule update --init --recursive
netlify dev
```