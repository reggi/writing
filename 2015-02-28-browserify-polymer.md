# Polymer / Vulcanize + ES6 (Babelify) + Browserify

_Saturday, February 28, 2015 at 9:39:05 PM_

Here's a little background context: I have one project that uses [RequireJS](http://requirejs.org/) and I just moved it to [Browserify](https://github.com/substack/node-browserify). This project also uses a [polymer](https://github.com/polymer/polymer) component.

[Vulcanize](https://github.com/polymer/vulcanize) is a comand-line-interface the works with polymer components, it concatenates them all into one file.  I noticed that it has a flag `--inline --csp`, which allows you to "Bundle all javascript (inline and external) into <output file name>.js".

The idea is to take this javascript file of the code for all your polymer components and add it to the rest of the site code with browserify. Then it hit me, during this process you could transpile
ES6 code to ES5 using [babelify](https://github.com/babel/babelify).

My dream would be the ability to create a component like this:

_Javascript_ : format-date.js

```
import Polymer from "polymer"
import moment from "moment"

Polymer("format-date", {
  ready: function() {
	this.time = moment(this.time, "YYMMDD").format()
  }
});
```
_Template_ : format-date.html

```
<polymer-element name="format-date" attributes="time">
  <template>
    {{ time }}
  </template>
  <script src="./format-date.js"></script>
</polymer-element>
```

_Usage_ 

```
<format-date time="010215"></format-date>
```

This would create a little bit of a problem with the ecosystem of how polymer components work.

When I publish this do I include a complied version of the javascript file? Including moment? Do I use `bower` and a [`browseify- shim`](https://github.com/thlorenz/browserify-shim) to include `moment`? Do I use `npm` to include `moemnt`?

Ideally If I vulcanized two components that both use `moment` as a dependency it's only included once.

## Where I got with this

I hit a wall where Babelify and browserify-shim didn't play well together. I posted an issue [thlorenz/browserify-shim#145](https://github.com/thlorenz/browserify-shim/issues/145) talking about it more in depth, and I published my code here [reggi/demo-browserify-polymer-es6](https://github.com/reggi/demo-browserify-polymer-es6).

## Ideal build script

Ideally I can get past the wall described above and have some scripts in `pacakge.json` that do this:

```
"scripts": {
    "vulcanize": "vulcanize format-date.html --inline --csp vulcanize.js",
    "browserify": "browserify vulcanized.js -t [browserify-shim babel] > main.js",
    "vulcanizeify": "nom run vulcanize && nom run browserify"
}
```

Then `nom run vulcanizeify` would do the following:

* concatenate all javascripts exporting `vulcanized.html` and `vulcanize.js` 
* browserify `vulcanize.js`
  * shims `polymer`
  * transpiles es6
  
 