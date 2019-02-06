### Session 6
<b>Feladat:</b>
Illesszünk be egy Pomodoro órát az adott kérdést mellé, ha a játékos válaszolt az óra álljon meg. Ha lejárt az idő ne tudjon már választ adni!

<hr>

Keressünk meg a <b>question-block</b> class-ú elemet és illesszük be mellé, vele egy szintre az svg-t ami mutatja majd az időt:


```html
<div class="col-md-3">
  <div class="progress-circle__container" v-if="userAnswers.length < questionForPlay">
    <span class="progress-circle__percent">{{ countDownTimer }}</span>
    <svg class="progress-circle" viewBox="0 0 106 106" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
      <g id="Page-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
        <g id="ProgressBar" transform="translate(-17.000000, -17.000000)">
          <circle id="Oval" stroke="#949494" stroke-width="5" fill-rule="nonzero" cx="70" cy="70" r="50"></circle>
          <path class="progress-circle__path" d="M70,120 C97.6142375,120 120,97.6142375 120,70 C120,42.3857625 97.6142375,20 70,20 C42.3857625,20 20,42.3857625 20,70 C20,97.6142375 42.3857625,120 70,120 Z" id="Oval-Copy" stroke-width="5" :stroke-dasharray="circle" fill-rule="nonzero" transform="translate(70.000000, 70.000000) rotate(-125.000000) translate(-70.000000, -70.000000) "></path>
        </g>
      </g>
    </svg>
  </div>
</div>
```

Adjuk a data objektumhoz a következő változókat:

```javascript
countDownTimer: 20,
circlePercent: 0,
```

A vue instanceban a data objektummal egy szinten hozzunk létre egy computed nevű property-t.

A computed property-k arra szolgálnak hogy alkalmazásukkal minnél kevesebb logika legyen a template-ben, illetve nem cache-elődik a tartalma, ezért alkalmazásukkal gyorsabbá tehető az alkalmazás. Mindig szükséges nekik visszatérési érték (return).

Ugyanúgy kell meghívni őket mint a data objektumban lévő propertyket, nem kell használni a zárójeleket hiába függvények.

```javascript
computed: {
  
}
```

Ide pedig ilesszük be a következő függvényt: 

```javascript
circle () {
  return ((this.circlePercent / 100) * 100 * Math.PI) + ',9999'
}
```

Adjuk a kódhoz a következő css-t:

```scss
.progress-circle {
  max-width:255px;
  max-height:255px;
  width:100%;
  transform: scaleX(-1) rotate(-55deg);

  &__percent {
    position:absolute;
    top:50%;
    left:50%;
    transform: translate(-50%,-50%);
  }

  &__container {
    display:inline-block;
    position:relative;
  }

  &__path {
    transition: 0.5s ease-in-out all;
    stroke: $green;
  }

}
```

Sikeresen megjelent a pomodoro óránk, most azt szeretnénk ha a <b>Show question</b> gombra kattintunk akkor induljon el az óra!

Ehhez deklaráljuk a következő property-ket a data objektumban:

```javascript
countDownSteppes: 20,
timerInterval: null,
counterState: false
```

hozzuk létre a következő 2 metódust, a startTimer() meghívja countDown() metódust, elindul az a pomodoro, utána pedig setInterval segítségével, újra ésújra meghívja 1 másodpercenként, tehát jár tovább a pomodoro:

```javascript
startTimer: function () {
  this.countDown()
  this.timerInterval = setInterval(this.countDown, 1000)
},
countDown: function () {
  let n = this.countDownTimer
  if (!this.counterState) {
    this.counterState = true
  } else if (n > 0) {
    n = n - 1
    this.countDownTimer = n
    this.circlePercent = this.circlePercent + (100 / this.countDownSteppes)
  }
},
```

A questionHandler() methódus 2. sorába illeszük be a startTimer() methódust:

```javascript
this.startTimer()
```

Tehát amikor a <b>Show question</b> gombra kattintanak, elindul a pomodoro.

Nézzük meg az alkalmazást!

A <b>Show question</b> gombra kattintva elindul a pomodoro, de hiába adunk tippet, nem áll még le az óra, ileltve ha ismét a <b>Show question</b> gombra kattintunk, nem nullázódik le a pomodoro, megy tovább!

Először az újraindulás problémáját oldjuk meg!

Hozzuk létre a következő metódust, ez leállítja a countDown függvény ismétlődő hívását a számlálót vissza állítja az eredeti állípotba a csíkot is "kinulláza":

```javascript
resetTimer: function () {
  clearInterval(this.timerInterval)
  this.counterState = false
  this.circlePercent = 0
  this.countDownTimer = this.countDownSteppes
}
```

Hívjuk meg a resetTiemr metódust a questionHandler() methódustban a következő képpen:

```javascript
questionHandler: function () {
  let questionId = this.randomQuestionsArr.pop() // next question
  this.resetTimer()
  this.startTimer()
  if (this.randomQuestionsArr.length + 1 < this.questionForPlay) {
    this.saveUserTip()
  }
  this.userTip = undefined // reset userTip
  if (questionId !== undefined) {
    this.getQuestionByCategory(this.selectedCategoryId, questionId)
  } else {
    /* after last tip hide question and possAnswers */
    this.actQuestion = ''
    this.possAnswers = []
  }
}
```

Hívjuk meg a resetCategory() methódusban is a resetTimer() methódust, tehát ha új játékot kezdünk akkor is álljon le a pomodoro 

```javascript
resetCategory: function () {
  this.selectedCategoryName = ''
  this.selectedCategoryId = 0
  this.userAnswers = []
  this.randomQuestionsArr = []
  this.actQuestion = ''
  this.possAnswers = []
  this.correctAnswer = ''
  this.questionPerCategory = 10
  this.questionForPlay = 5
  this.resetTimer()
}
```

Nézzük meg az alkalmazást!

Láthatjuk ahogy kérdést kapunk elindul az óra, hiába tippelünk még nem áll meg a pomodoro, de ha ismételten a <b>Show question</b> gombra kattintunk akkor ismételten újra fog indulni a pomodoro. 

Most oldjuk meg hogy állítsuk le az órát ha a játékos tippel valamire:

ehhez hozzuk létre a stopTimer() metódust:

```javascript
stopTimer: function () {
  this.counterState = false
  clearInterval(this.timerInterval)
}
```

Majd hívjuk meg a getUserTip() methódustban:

```javascript
getUserTip: function (answerId) {
  this.setUserTip(answerId)
  this.stopTimer()
}
```

Most azt kell megoldanunk, ha alejárt az idő a játékos ne tudjon tippet adni, hiába kattint akármelyik jelölőnégyzetre:

A következő két property értékét állítsuk 5-re, mindkettő a data objektumban található :

```javascript
countDownTimer: 5,
countDownSteppes: 5,
```

Nézzük meg az alkalmazást!

Láthatjuk ha lejárt az idő még tudunk tippelni!

Ezen problémát megoldandó hozzunk létre 2 property-t a data objektumban:

```javascript
userCanAddTip: true,
userAddedTip: false,
```

A setUserTip() methódost módosítsuk a következőre:

```javascript
setUserTip: function (tip) {
  if (this.userAddedTip === false) {
    if (this.userCanAddTip === false) {
      this.userTip = undefined
    } else {
      this.userTip = tip
    }
  }
}
```

A saveUserTip() methódost módosítsuk a következőre:

```javascript
saveUserTip: function () {
  if (this.userAddedTip === false) {
    this.userAnswers.push('null')
  } else {
    if (this.userTip === undefined) {
      this.userAnswers.push('null')
    } else {
      if (this.userTip === this.correctAnswer) {
        this.userAnswers.push('true')
      } else {
        this.userAnswers.push('false')
      }
    }
  }
  this.userCanAddTip = true
  this.userAddedTip = false
}
```

A stopTimer() methódost módosítsuk a következőre:

```javascript
stopTimer: function () {
  this.counterState = false
  this.userCanAddTip = false
  this.userAddedTip = true
  clearInterval(this.timerInterval)
}
```

A countDown() metódust pedig a következőre:

```javascript
countDown: function () {
  let n = this.countDownTimer
  if (!this.counterState) {
    this.counterState = true
  } else if (n > 0) {
    n = n - 1
    this.countDownTimer = n
    this.circlePercent = this.circlePercent + (100 / this.countDownSteppes)
  } else {
    this.resetTimer()
    this.userCanAddTip = false
  }
}
```

Nézzük meg az alkalmazást!

Ha lejár az idő hiába próbálunk tippelni már nem sikerül, ha a <b>Show question-re</b> kattintunk akkor a progressbaron fekete lesz a pötty tehát skipped értéket kap

Feladat teljesítve!
