# example-self-mutating-dat-site

Example of self-mutating websites using the Dat APIs in Beaker

The interesting code:

```js
var selfArchive = new DatArchive('' + window.location)
setup()

async function setup () {
  // get info about self
  var info = await selfArchive.getInfo()
  console.log('About me', info)

  // setup the UI
  var mutateBtn = document.querySelector('#mutate-btn')
  document.querySelector('#site-state').textContent = (info.isOwner) ? 'editable' : 'read-only'
  if (!info.isOwner) {
    // not mutable, tell the user
    mutateBtn.classList.add('disabled')
    document.querySelector('#readonly-instructions').textContent = `
      Fork me to make a mutatable copy.
    `
  } else {
    // mutable, add mutate action
    mutateBtn.classList.remove('disabled')
    mutateBtn.addEventListener('click', onMutate)
  }
  document.querySelector('#fork-btn').addEventListener('click', onFork)
}

async function onMutate (e) {
  e.preventDefault()
  var selfMutationsEl = document.querySelector('#self-mutations')

  // add a timestamp
  var tsEl = document.createElement('p')
  tsEl.textContent = `Self-mutated at ${(new Date()).toLocaleString()}`
  selfMutationsEl.appendChild(tsEl)

  // write self
  await selfArchive.writeFile('/index.html', document.documentElement.outerHTML, 'utf8')
}

async function onFork (e) {
  e.preventDefault()

  // prompt user
  var selfForkArchive = await DatArchive.fork(selfArchive)
  console.log('done', selfForkArchive.url)
  window.location = selfForkArchive.url
}
```
