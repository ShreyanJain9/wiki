+++
+++

# stuff i learned by me fucking around with sveltekit for a bit

{{<toc>}}

## prerequisites

```sh
npm i -D @sveltejs/adapter-static  # for static file output
npm i -D svelte-preprocess  # to allow for <style global>
npm i -D html-minifier  # to minify html/css/js output
```

## allow global styles

you need to install and initialize svelte-preprocess instead of vitePreprocess if you want to use `<style global>` instead of `:global(selector)` for every single selector

svelte.config.js:

```js
import adapter from '@sveltejs/adapter-static';
import preprocess from 'svelte-preprocess';

/** @type {import('@sveltejs/kit').Config} */
const config = {
	kit: {
		adapter: adapter()
	},
	preprocess: [preprocess()]
};

export default config;
```

## static site generation with zero javascript

enable prerendering (keep ssr enabled) to have a static build. if you turn off ssr then your build will be empty

however you can turn off csr to disable routing, and this results in a zero-js build output

src/routes/+layout.js:

```js
import {dev} from '$app/environment'

export const prerender = true;
export const csr = dev;
```

## minifying

if you wanna minify the build output you can create a Handle hook to minify each page before it is server-side-rendered

src/hooks.server.js:
```js
import { minify } from 'html-minifier';
import { building } from '$app/environment';
 
const minification_options = {
  collapseBooleanAttributes: true,
  collapseWhitespace: true,
  conservativeCollapse: true,
  decodeEntities: true,
  html5: true,
  ignoreCustomComments: [/^#/],
  minifyCSS: true,
  minifyJS: true,
  removeAttributeQuotes: true,
  removeComments: false, // some hydration code needs comments, so leave them in
  removeOptionalTags: true,
  removeRedundantAttributes: true,
  removeScriptTypeAttributes: true,
  removeStyleLinkTypeAttributes: true,
  sortAttributes: true,
  sortClassName: true
};
 
/** @type {import('@sveltejs/kit').Handle} */
export async function handle({ event, resolve }) {
  let page = '';
 
  return resolve(event, {
    transformPageChunk: ({ html, done }) => {
      page += html;
      if (done) {
        return building ? minify(page, minification_options) : page;
      }
    }
  });
}
```