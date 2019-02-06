### Session 2
<b>Feladat:</b>
Rendom módon az adott kategória json fájl-jából válasszunk ki 5 kérdést és ezeket jelenítsük is meg. 
Mindig random kérdések jelenjenek meg, kettő ugyanolyan sose!

<hr>

```hint:```

```nyissuk meg a history.json-t és vizsgáljuk meg```

Hozzunk létre a data objektumban három változót azt egyik legyen <b>questionForPlay</b> a másik <b>questionPerCategory</b> a harmadik
<b>randomQuestionsArr</b> 

<b>questionForPlay:</b> hány kérdés jelenjen meg a kvíz során

<b>questionPerCategory:</b> hány kérdés közül válogasson kategóriánként

<b>randomQuestionsArr:</b> a random módon kiválaszott kérdések indexe

```javascript
questionPerCategory: 10,
questionForPlay: 5,
randomQuestionsArr: [],
```

Hozzuk létre egy függvényt a method-okhoz, amely kiválaszt 5 random számot, egy 0-9-ig tartó intervallumból, két ugyanazt a számot nem választhatjuk ki!

```javascript
getRandomQuestions: function () {
  let arr = []
  while (arr.length < this.questionForPlay) {
    let r = Math.floor(Math.random() * this.questionPerCategory)
    if (arr.indexOf(r) === -1) arr.push(r)
  }
  this.randomQuestionsArr = arr
},
```

Ezt a függvényt iratkoztassuk fel a kategóriákra:

```html
@click="getCategory(category.id,category.name),getRandomQuestions()"
```

Jelenítsük meg a random számok tömbjét a <b>Reset quiz</b> gomb fölött:

```html
<div>{{randomQuestionsArr}}</div>
```

Oldjuk meg, hogy a <b>Reset quiz</b> gombra kattintva ennek a tömbnek a tartalma is törlődjön, egészítsük ki a <b>resetCategory()</b> függvényt a következő sorral: 

```javascript
this.randomQuestionsArr = []
```

Hozzunk létre egy gombok ami rotálni fogja a kérdéseket. 
A felirata legyen <b>Start quiz</b>

A rotációt úgy érjük el hogy a randomQuestionsArr,ből kivesszük az utolsó elemet a <b>Start quiz</b> gombra kattintva

Ez a gomb akkor jelenjen meg amikor már kiválasztottuk a kategóriát!


```html
<div class="m-2">
  <button
    @click="questionHandler()"
    class="btn btn-info">
    Show question
  </button>
</div>
```

```html
questionHandler: function () {
  this.randomQuestionsArr.pop()
}
```

Nézzük meg mi történik!

Hozzunk létre egy függvényt amely képes arra hogy a kiválasztott kategória adott indexű kérdését, és annak válaszait megjeleníti a template-ben

Módosítsuk a questionHandler() függvényt a következő képpen:

```javascript
questionHandler: function () {
  let questionId = this.randomQuestionsArr.pop()
  if (questionId !== undefined) {
    this.getQuestionByCategory(this.selectedCategoryId, questionId)
  } else {
    /* after last tip hide question and possAnswers */
    this.actQuestion = ''
    this.possAnswers = []
  }
}
```

Mint láthatjuk létre kell hoznunk egy függvényt ami kiolvassa az adott kategória json-jéből a kérdéseket, és ezeket majd tároljuk a <b>actQuestion</b> és a <b>possAnswers</b> változókban, ezek fognak majd megjelenni a templateben

Deklaráljuk a két változót a data objektumban
```javascript
actQuestion: '',
possAnswers: []
```

Hozzuk létre a getQuestionByCategory() methódust

```javascript
getQuestionByCategory: function (categoryId, questionId) {
  let questionJson = []
  this.actQuestion = ''
  this.possAnswers = []
  switch (categoryId) {
  case 1: {
    questionJson = this.questionScience
    break
  }
  case 2: {
    questionJson = this.questionHistory
    break
  }
  case 3: {
    questionJson = this.questionGastronomy
    break
  }
  case 4: {
    questionJson = this.questionMovies
    break
  }
  case 5: {
    questionJson = this.questionBooks
    break
  }
  default: {
    questionJson = this.questionScience
    break
  }
  }
  this.actQuestion = questionJson[questionId]['question']
  for (let i = 0; i < 4; i++) {
    this.possAnswers.push({
      item: questionJson[questionId]['answers'][i]['item'],
      id: questionJson[questionId]['answers'][i]['id']})
  }
  this.correctAnswer = questionJson[questionId]['correct']
}
```

A <b>correctAnswer</b> -t is deklaráljuk a data objektumban:

```javascript
correctAnswer: '',
```

Nézzük meg a Vue devtoolsban, hogy az újonnan deklarált 3 változó kap-e értéket és ahogy a <b>Show question</b> gombra kattintunk úgy rotálódnak-e a kérdések?!


A resetCategory()-t egészítsük ki a következő 3 sorral:

```javascript
this.actQuestion = ''
this.possAnswers = []
this.correctAnswer= ''
```
Ezen sor alatt:
```html
<div>{{randomQuestionsArr}}</div>
```
Jelenítsük meg az aktuális kérdést a v-html direktíva segítségével:

```html
<div class="col-md-12">
  <h2 v-html="actQuestion"></h2>
</div>
```

Jelenítsük meg az aktuális kérdés alatt a lehetséges 4 választ, v-for-t használjunk, jelenítsük is meg az indexeket!

```html
<div class="question-block col-md-9">
  <div class="text-center">
    <ul style="width: 300px; display:inline-block;" class="mt-5">
      <li
        class="text-left mb-3 d-flex align-items-center"
        v-for="(answer, i) in possAnswers"
        :key="answer.id">{{i+1}}. {{answer.item}}
      </li>
    </ul>
  </div>
</div>
```
