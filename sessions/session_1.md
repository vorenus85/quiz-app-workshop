### Session 1
<b>Írassuk ki az 5 témakört, bármelyik kategóriára kattintva jelenjen meg, hogy mely kategóriát választottuk ki</b>

<b>1.1.</b> A Main.vue script részében importáljuk be a kategóriák json fájljait:

```
import booksJson from '../json/books.json'
import gastronomyJson from '../json/gastronomy.json'
import historyJson from '../json/history.json'
import moviesJson from '../json/movies.json'
import scienceJson from '../json/science.json'
```

<b>1.2.</b> A data objektumban tároljuk őket 1-1 változóban:

```angular2html
questionScience: scienceJson,
questionHistory: historyJson,
questionGastronomy: gastronomyJson,
questionMovies: moviesJson,
questionBooks: booksJson,
```

<b>1.3.</b> A kategóriák legyenek buttonok, és minden kategóriának legyen különböző színe például a bootstrap framework főbb állapot színei (primary, info, warning, success, danger)

Ehhez a data objektumban hozzunk létre egy <b>categories</b> objektumot:

```angular2html
categories: [
    {
      id: 1,
      name: 'Science and technology',
      color: 'primary'
    },
    {
      id: 2,
      name: 'History',
      color: 'info'
    },
    {
      id: 3,
      name: 'Gastronomy',
      color: 'warning'
    },
    {
      id: 4,
      name: 'Movies',
      color: 'success'
    },
    {
      id: 5,
      name: 'Books',
      color: 'danger'
    }
  ],
```

<b>1.4.</b> A template részt töröljük ki teljesen, a helyére ezt a html tegyük be:

```angular2html
<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-12 mt-5">
          <div id="choose-category">
            <h1>Choose category</h1>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
```

<b>1.5.</b> For ciklussal jelenítsük meg a data objektumben deklarált categories objektum elemeit. Gombokként jelenjenek meg, a gombok színei az objektumban deklarált színek legyenek! Használjuk a key direktívát a for ciklushoz


```angular2html
<button
  v-for="category in categories"
  :class="['btn m-2 btn-' + category.color]"
  :key="category.id">
  {{ category.name }}
</button>
```

<b>1.6.</b> A gombokra kattintva szeretnénk ha megjelenne az adott kategória neve, ezzel együtt tűnjenej el a kategóriák!

Hozunk létre két változót a data objektumban <b>selectedCategoryName</b> és <b>selectedCategoryId</b> néven ezek lesznek az adott kategória neve és idje, a selectedCategoryName üres string legyen, a selectedCategoryId legyen 0-a

```angular2html
selectedCategoryName: '',
selectedCategoryId: 0
```

Hozzunk létre a methods objektumot a vue instance-ban azon belül pedig hozzunk létre egy getCategory() nevű függvényt, a fügvény 2 paramétert kapjon meg a kategória idjét és nevét

```angular2html
methods: {
  getCategory: function (id, name) {
    this.selectedCategoryName = name
    this.selectedCategoryId = id
  }
}
```  

Hozzuk létre a click eventet a gombokon

```angular2html
@click="getCategory(category.id,category.name)"
```

A <b>choose-category</b> div alá hozzuk létre a következő divet, amiben megjelenítjük a kiválasztott kategóriát és annak id-jét

```angular2html
<div id="questions">
  <h4>Choosen category: {{selectedCategoryName}}</h4>
  <h4>Choosen id: {{selectedCategoryId}}</h4>
</div>
```

Oldjuk meg hogy van az 5 kategória gombja látszik vagy kiválaszott kategória neve és idje, <b>Conditional Rendering</b> -et  <b>v-if</b> és <b>v-else</b> 

```angular2html
<div id="choose-category" v-if="selectedCategoryId === 0">
...
...
</div>
<div id="questions" v-else>
...
...
</div>
```

Hozzuk létre egy gombot <b>Reset Quiz</b> néven amire kattintva reseteljük a kiválasztott kategóriát, tehát újra tudunk majd kategóriát választani

Hozzuk létre a gombok a template aljára

```angular2html
<div class="m-2">
  <button
    @click="resetCategory()"
    class="btn btn-danger m-3">
    Reset Quiz
  </button>
</div>
```

Ahogy láthatjuk ez is clickre eventre fog működni, ehhez hozzuk létre a resetCategory() methodust:

```angular2html
resetCategory: function () {
  this.selectedCategoryName = ''
  this.selectedCategoryId = 0
}
```
