# example-self-mutating-dat-site

Example of self-mutating websites using the Dat APIs in Beaker

The interesting code:

```js
/* globals DatArchive */

var selfArchive = new DatArchive(window.location.toString())

async function setup () {
  // get info about self
  var info = await selfArchive.getInfo()

  // setup the UI
  // ... (wire up onMutate and onFork here. see index.js for full code) ...
}

async function onMutate (e) {
  // add a timestamp
  var selfMutationsEl = document.querySelector('#self-mutations')
  var tsEl = document.createElement('p')
  tsEl.textContent = `Self-mutated at ${(new Date()).toLocaleString()}`
  selfMutationsEl.appendChild(tsEl)

  // write self
  await selfArchive.writeFile('/index.html', document.documentElement.outerHTML, 'utf8')
}

async function onFork (e) {
  // prompt user
  var selfForkArchive = await DatArchive.fork(selfArchive)
  window.location = selfForkArchive.url
}

// Go!!
setup()
```
