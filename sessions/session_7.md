### Session 7
<b>Feladat:</b>
Tudja meg a játékos, hogy milyen eredményt ért el! Ha válaszolt az utolsó kérdésre is akkor a <b>Show question</b> gomb szövege változzon <b>Show result</b>-ra és rá kattintva ugorjon fel egy popup, amiben kiiírjuk a százalékos eredményt, egy vicces mondatot, és egy gifet jelenítünk meg!

A result.json fájlban találhatóak az eredménytől függő Megjegyzést illetve a gifek elérési útjai!

<hr>

Először importáljuk be a result.json-t:

```javascript
import resultJson from '../json/result.json'
```

Majd be a result.jsont a data objektumba hogy tudja használni a vue instance:

```javascript
resultCategories: resultJson,
```

Keressük meg a tempalte-ben azt a gombot ahol meghívjuk a questionHandler() metódust, változtassuk meg a következő módon:


```html
<button
  @click="questionHandler()"
  class="btn btn-info">
  {{questionBtnText}}
</button>
```

A <b>questionBtnText</b> egy computed property lesz, hozzuk létre!

```html
questionBtnText () {
  return this.userAnswers.length === this.questionForPlay ? 'Show my result!' : (this.randomQuestionsArr.length === this.questionForPlay ? 'Start Quiz!' : 'Next Question!')
}
```

A questionHandler() metódust a következőképp módosítsük:

```html
questionHandler: function () {
  if (this.userAnswers.length !== this.questionForPlay) {
    /* get question */
    let questionId = this.randomQuestionsArr.pop()
    this.resetTimer()
    this.startTimer()
    if (this.randomQuestionsArr.length + 1 < this.questionForPlay) {
      this.saveUserTip()
    }
    this.userTip = undefined
    if (questionId !== undefined) {
      this.getQuestionByCategory(this.selectedCategoryId, questionId)
    } else {
      /* after last tip hide question and possAnswers */
      this.actQuestion = ''
      this.possAnswers = []
    }
  } else {
    this.calculateResult()
    this.showModal()
  }
}
```

A hogy láthatjuk a legalsó else ágban létre kell hoznunk két új metódust, az egyik az eredményt fogja kiszámolni, a másik megjeníti a popupot amiben az eredmény lesz


az eredmény számításért felelő metódus:
```javascript
calculateResult: function () {
  let countOfTrue = this.userAnswers.filter(x => x === 'true')
  this.resultPercentage = Math.round((countOfTrue.length / this.questionForPlay) * 100)
  let resultCategoriesJson = this.resultCategories
  switch (true) {
  case (this.resultPercentage === 100):
    this.resultTitle = resultCategoriesJson[0]['title']
    this.resultImgSrc = resultCategoriesJson[0]['gifs'][0]['src']
    break
  case (this.resultPercentage >= 80):
    this.resultTitle = resultCategoriesJson[1]['title']
    this.resultImgSrc = resultCategoriesJson[1]['gifs'][0]['src']
    break
  case (this.resultPercentage >= 60):
    this.resultTitle = resultCategoriesJson[2]['title']
    this.resultImgSrc = resultCategoriesJson[2]['gifs'][0]['src']
    break
  case (this.resultPercentage >= 40):
    this.resultTitle = resultCategoriesJson[3]['title']
    this.resultImgSrc = resultCategoriesJson[3]['gifs'][0]['src']
    break
  case (this.resultPercentage >= 20):
    this.resultTitle = resultCategoriesJson[4]['title']
    this.resultImgSrc = resultCategoriesJson[4]['gifs'][0]['src']
    break
  default:
    this.resultTitle = resultCategoriesJson[5]['title']
    this.resultImgSrc = resultCategoriesJson[5]['gifs'][0]['src']
    break
  }
}
```

A popup megjelenítésért felelő metódus:

```javascript
showModal: function () {
  this.$refs.myModalRef.show()
}
```

A popup elrejtésért felelő metódus:

```javascript
hideModal: function () {
  this.$refs.myModalRef.hide()
}
```

Hozzunk létre a data objektumban néhány új propertyt:

```javascript
resultTitle: '',
resultImgSrc: '',
resultPercentage: 0
```

Ileeszük be a templatebe a modalt:

```html
<b-modal ref="myModalRef" hide-footer title="Your result">
  <div class="d-block text-center">
    <h3>{{resultTitle}}</h3>
    <h4><strong>{{resultPercentage}}%</strong></h4>
    <img class="img-fluid" :src="getIconPath(resultImgSrc)" alt="">
  </div>
  <b-btn class="mt-3" variant="outline-danger" @click="hideModal">Hide result</b-btn>
</b-modal>
```

Ahogy láthatjuk a képnél használjuk az :src direktívát mivel dinamikusan kell beillesztenünk a képet, eredménytől függően, ehhez szükségünk lesz még egy metódusra:

```javascript
getIconPath (iconName) {
  return iconName ? require(`@/assets/${iconName}`) : ''
}
```

Azért nem a static mappába tettük a képeket, mert onann a webpack nem képes feldolgozni a dinamukus elérési utakat. Itt az eredménytől függ a felhasznált kép, ezért dinamikusan ilelsztjük be a kép elérési útját ezért kell a  getIconPath() methódust használni a fenti képpen!
