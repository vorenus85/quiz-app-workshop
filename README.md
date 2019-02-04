# quiz-app-workshop

> A Vue.js project

# Quiz-app specifikáció

- Választhassunk több témakör közül
- Az adott témakör kérdéseire 4 válasz lehetőség legyen
- A témakörökből random módon kapjuk mindig a kérdéseket
- Állítható legyen hány kérdésből válasszuk ki a random kérdéseket a játékhoz
- Állítható legyen hány kérdést kapjunk a játék során
- A válaszadásra maximum 20 másodpercünk legyen, ezután ne lehessen választ adni
- Csak egy helyes válasz van
- Egyszer lehessen csak tippelni kérdésenként
- Legyen egy progress bar ahol láthatjuk, hogy haladunk (true-tip, false-tip, skip-tip) 
- A játék végén százalákosan láthassuk az eredményt, + egy-egy vicces kisérőszöveg és animáció jelenjen meg a teljesítménytől függően

## Json mappa
Ebben a mappában találhatóak az egyes kategóriák (books.json, gastronomy.json, history.json, movies.json, science.json), és azok kérdései.

Illetve az elért teljesítményhez a vicces kísérőszövegek és az animációk elérési útjai (result.json).

## Assets
A rate_0, rate_20, rate_40, rate_60, rate_80, rate_100 mappákban találhatóak az animációk gif fájljai.

#### Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
