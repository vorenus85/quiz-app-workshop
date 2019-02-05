### Session 4

Készítsünk egy progress bart ahol nyomon követhetjük a haladásunkat. 
Ez jelenítse meg összesen hány kérdés lesz a Kvíz során, jelenleg fixen 5. 
Ha a játékos nem ad választ a kérdésre legyen a jelőlő fekete, ha helyes választ ad akkor zöld, ha helytelent akkor piron legyen a színe.


Az aktuális kérdés felé illeszzük be a következő kódot, ez 5 db jelölőt fog megjeleníteni:

```angular2html
<div class="question-progressbar d-flex mt-3 mb-3 justify-content-between align-items-center">
  <span class="question-progressbar-item"
        v-for="(question) in questionForPlay"
        :key="question.id"
  ></span>
</div>
```
Illesszük be a css-ét is:

```angular2html
.question-progressbar {
  position: relative;
  width: 500px;
  margin: 0 auto;

  &-item {
    width: 20px;
    height: 20px;
    border-radius: 100%;
    background: $gray-lighter;
    box-shadow: 0 0 10px 0 rgba(0,0,0,.3);
    position: relative;
    z-index: 2;
    font-size: 12px;
  }

  &:before {
    content: '';
    background: $gray-light;
    width: 100%;
    height: 1px;
    display: inline-block;
    position: absolute;
    z-index: 1;
  }

  .user-tip {
    &-skip {
      background: $gray;
      color: $white;
    }
    &-true {
      background: $green;
      color: $white;
    }
    &-false {
      background: $red;
      color: $white;
    }
  }
}
```

Hosszuk létre egy tömböt a data objektumban, amiben a játékos válaszait fogjuk tárolni:

```
userAnswers: [],
```

Hozzuk létre a függvényt ami elmenti a válaszokat:


```
saveUserTip: function () {
  if (this.userTip === undefined) { // if user not choose any tip
    this.userAnswers.push('null')
  } else {
    if (this.userTip === this.correctAnswer) {
      this.userAnswers.push('true')
    } else {
      this.userAnswers.push('false')
    }
  }
}
```

A tippek akkor mentődjenek el a tömbbe amikor rotálunk a következő kérdésre, ezzel egy időben fog majd frissülni is a progressbar.
Ennek megfelelően a <b>questionHandler()</b> függvényt módosítsük a következőképp:

```angular2html
questionHandler: function () {
  let questionId = this.randomQuestionsArr.pop() // next question
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

Nézzük meg a vue devtoolsban hogy be kerülnek a tippek logikai értékei

Ha helyesen bekerülnek akkor már csak szinezni kell a progressbarunkat, tehát az egyes itemek kapják meg a megfelelő class-t:


A <b>question-progressbar-item</b> elemet egészítsük ki egy index nevű indexxel a v-for direktívánál. 
Hozzuk létre a :class direktívát ahol a jelölőnégyzetekhez hasonlóan egy függvényhívás fogja visszaadni az elem "színét"
```angular2html
<span class="question-progressbar-item"
      v-for="(question, index) in questionForPlay"
      :class="progressBarValidation(userAnswers[index])"
      :key="question.id"
></span>
```

A progressBarValidation methódus a kövtkező képpen nézzen ki:

```angular2html
progressBarValidation: function (userAnswer) {
  return userAnswer !== undefined ? (userAnswer === 'null' ? 'user-tip-skip' : (userAnswer === 'true' ? 'user-tip-true' : 'user-tip-false')) : ''
}
```
