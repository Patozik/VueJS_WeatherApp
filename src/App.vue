<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp < 16 ? 'cold' : ''">
    <main>
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Szukaj miasta ..." 
          v-model="query"
          @keypress.enter="fetchWeather"
        />
      </div>

      <div class="error">{{ ErrorMsg }}</div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°C</div>
          <div class="weather">{{ weatherTranslation() }}</div>
        </div>
      </div>

    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      api_key: process.env.VUE_APP_API,
      url_base: process.env.VUE_APP_URL,
      query: '',
      weather: {}
    }
  },
  mounted:function(){
    this.query="Rzeszów";
    this.fetchWeather();
    this.query="";
  },
  methods: {
    async fetchWeather () {
        let response = await fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`);
        let json = await response.json();
        this.weather = await json;

        if (json.cod == 404) {
          return this.ErrorMsg = "Nie znaleziono miasta: " + this.query;
        } else if (json.cod == 400) {
          return this.ErrorMsg = "Wpisz nazwę miasta ";
        } else {
          return this.ErrorMsg = "";
        }
      
    },
    dateBuilder () {
      let d = new Date();
      let months = ["Styczeń", "Luty", "Marzec", "Kwiecień", 
      "Maj", "Czerwiec", "Lipiec", "Sierpień", 
      "Wrzesień", "Październik", "Listopad", "Grudzień"];
      let days = ["Niedziela", "Poniedziałek", "Wtorek", 
      "Środa", "Czwartek", "Piątek", "Sobota"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
    weatherTranslation () {

      const weatherArray = [
        {
          english: 'Rain',
          polish: 'Deszcz'
        },
        {
          english: 'Clouds',
          polish: 'Chmury'
        },
        {
          english: 'Clear',
          polish: 'Czyste Niebo'
        },
        {
          english: 'Mist',
          polish: 'Mgła'
        }
      ]

      const w = this.weather.weather[0].main;

      const translatedWord = weatherArray.find((word) => {
        return word.english == w;
      });

      return translatedWord.polish;
    }
  }
}
</script>

<style>

@font-face {
  font-family: myFont;
  src: url('fonts/Montserrat-Italic-VariableFont_wght.ttf');
} 

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'myFont', sans-serif;
}

#app {
  background-image: url('./assets/warm-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.5s;
}

#app.cold {
  background-image: url('./assets/cold-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.1), rgba(0, 0, 0, 0.75));
}

.error {
  color: #FFF;
  font-size: 32px;
  font-weight: 600;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.4);
  border-radius: 0px 16px 0px 16px;
  transition: 0.5s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 32px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius:16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 600;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.3);
  border-radius: 24px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color:#FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

</style>
