# wasmCloud.dev
Repository for the wasmCloud documentation site. This site is built with [Hugo](https://gohugo.io/) and uses the [godocs-1](https://github.com/wasmCloud/wasmcloud-dev-site/tree/main/themes/godocs-1) theme. 

## Running the site locally
Ensure you have the [hugo CLI](https://gohugo.io/getting-started/installing/) installed. Once you have the binary installed you can simply run `hugo serve -D` and access the local site in your browser at http://localhost:1313.

## Important files / folders

- [content](https://github.com/wasmCloud/wasmcloud-dev-site/tree/main/content) contains all of our content for the site like about, blogs, documentation, etc. Some of the pages in here are not enabled on the site
- The main scss file for website styles is located under the [resources](https://github.com/wasmCloud/wasmcloud-dev-site/tree/main/resources/_gen/assets/scss/scss) folder
- All static assets are served from the [static](https://github.com/wasmCloud/wasmcloud-dev-site/tree/main/static) folder.
- The [godocs-1](https://github.com/wasmCloud/wasmcloud-dev-site/tree/main/themes/godocs-1) directory includes theme information