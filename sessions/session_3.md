### Session 3
<b>Feladat:</b>
A kérdések előtt jelenjen meg egy szürke négyzet amire kattintva, egyből el is szineződik hogy jó-e a válasz vagy nem. Tippet csak egyszer lehessen megadni!

Helyezzük el a jelölő elemet, ez egy span lesz: 

<hr>

```html
<li
  class="text-left mb-3 d-flex align-items-center"
  v-for="(answer, i) in possAnswers"
  :key="answer.id">
  <span class="question-checkbox mr-3"></span>{{i+1}}. {{answer.item}}
</li>
```
Adjunk neki egy kis stílus formázást is:

```scss
.question {
  &-checkbox {
    width: 50px;
    height: 50px;
    background: $gray-light;
    border-radius: 10px;
    display: inline-block;
    cursor: pointer;
  }
}
```

Az előbb behelyezett jelölő négyzetet egészítsük ki egy @click eventtel, ez fogja figyelni mire tippert a játékos

```html
<span class="question-checkbox mr-3" @click="getUserTip(answer.id)"></span>
```

Hozzuk létre a getUserTip() függvényt ami paraméterként megkapja a válasz id-jét

```javascript
getUserTip: function (answerId) {
  this.setUserTip(answerId)
}
```

Hozzunk létre egy setUserTip() függvényt, ez paraméterként megkapja majd a válasz idjét, és elmenti a data objektum egy változójába <b>userTip</b> a játékos tipjét.

```javascript
userTip: undefined,
```

```javascript
setUserTip: function (tip) {
  if (this.userTip === undefined) { // to prevent secong tip chance
    this.userTip = tip
  }
}
```

A questionHandler() függvényt egészítsük ki a következő sorral, hogy kérdés rotálásnál törlődjön az előző kérdés tippje:

```javascript
questionHandler: function () {
  this.userTip = undefined
  ...
  ...
}
```

Mivel mikor megjelenik egy kérdés már tudjuk, hogy mi a helyes válasz <b>correctAnswer</b> és most már tudjuk, hogy mit tippelt a játékos, így validálhatjuk a választ, ha sikeres legyen a jelölő zöld, ha nem piros. 

Ehhez adjuk a kódhoz a következő css kódot:

```scss
.wrong .question-checkbox{
  background: $red;
}

.correct .question-checkbox{
  background: $green;
}
```

A válaszok lista elemeit:

```html
<li
  class="text-left mb-3 d-flex align-items-center"
  v-for="(answer, i) in possAnswers"
  :key="answer.id">
  <span class="question-checkbox mr-3" @click="getUserTip(answer.id)"></span>{{i+1}}. {{answer.item}}
</li>
```

Egészítsük ki a egy :class direktívával:

```javascript
:class="answerValidation(answer.id)"
```

Ez egy függvényt hív meg tehát írjuk is meg a validáló függvényt:

```javascript
answerValidation (answerId) {
  return this.userTip === answerId ? (this.userTip === this.correctAnswer ? 'correct' : 'wrong') : ''
}
```
