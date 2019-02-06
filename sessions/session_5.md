### Session 5
<b>Feladat:</b>
Készítsünk egy settings boardot az alkalmazáshoz. Ahol kiválaszthatjuk mennyi kérdésből kapjuk meg a random kérdéseket illetve hogy egy játék során hány kérdés jelenjen meg. 

Itt a <b>v-model</b> direktívát fogjuk használni , ez által lesz lehetséges a <b>two-way data bindings</b>


Másoljuk a következő kódot a kategória választó gombok alá:

```html
<h3 class="mt-3 mb-3">Quiz settings</h3>
<div class="settings-board">
  <div class="form-group d-flex align-items-center">
    <label for="questionPerCategory" style="width: 250px" class="text-left">Possible question per category:</label>
    <input
      id="questionPerCategory"
      type="number"
      min="10"
      max="20"
      class="form-control ml-3"
      style="width: 70px"
      v-model="questionPerCategory"
      readonly>
    <button class="btn btn-info ml-2" @click="questionPerCategoryInc()">+</button>
    <button class="btn btn-info ml-2" @click="questionPerCategoryDec()">-</button>
  </div>
  <div class="form-group d-flex align-items-center">
    <label for="questionPerCategory" style="width: 250px" class="text-left">Round per category:</label>
    <input
      id="questionForPlay"
      type="number"
      min="5"
      max="10"
      class="form-control ml-3"
      style="width: 70px"
      v-model="questionForPlay"
      readonly>
    <button class="btn btn-info ml-2" @click="questionForPlayInc()">+</button>
    <button class="btn btn-info ml-2" @click="questionForPlayDec()">-</button>
  </div>
  <h5>Possible question per category: <strong>{{questionPerCategory}}</strong></h5>
  <h5>Round per category: <strong>{{questionForPlay}}</strong></h5>
</div>
```

Illesszük be a következő css-t:

```scss
.settings-board {
  width: 400px;
  margin: 0 auto;
}
```

Hozzunk létre a data objektumban négy új változót:

```javascript
questionPerCategoryMax: 20,
questionPerCategoryMin: 10,
questionForPlayMax: 10,
questionForPlayMin: 5,
```

Illesszük be lenti 4 methódust:

```javascript
questionPerCategoryInc: function () {
  if (this.questionPerCategory <= this.questionPerCategoryMax - 1 && this.questionPerCategory >= this.questionPerCategoryMin - 1) {
    this.questionPerCategory++
  }
},
questionPerCategoryDec: function () {
  if (this.questionPerCategory - 1 <= this.questionPerCategoryMax && this.questionPerCategory - 1 >= this.questionPerCategoryMin) {
    this.questionPerCategory--
  }
},
questionForPlayInc: function () {
  if (this.questionForPlay <= this.questionForPlayMax - 1 && this.questionForPlay >= this.questionForPlayMin - 1) {
    this.questionForPlay++
  }
},
questionForPlayDec: function () {
  if (this.questionForPlay - 1 <= this.questionForPlayMax && this.questionForPlay - 1 >= this.questionForPlayMin) {
    this.questionForPlay--
  }
}
```

Végül illesszük be a resetCAtegory() methódushoz a következő két sort:
```javascript
this.questionPerCategory = 10
this.questionForPlay = 5
```

Így majd új játéknál a settings boardon a default értékek fognak megjelenni, nem azok amiket az előző játék során beállítottak.


Láthatjuk, hogy az input elemeken lévő v-modelnek köszönhetően, reaktív módon változik a kiírt szöveg is, ha növeljük a <b>Round per category</b> beállítást.

A <b>Round per category</b> beállítást növeljük mondjuk 8-ra, és válasszunk kategóriát.

Láthatjuk hogy 8 kis pötty jelent a progressbaron, illetve láthatjuk hogy a tömbben is 8 kérdés indexe található
