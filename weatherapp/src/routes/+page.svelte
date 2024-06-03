<script>
  let city = '';
  let weatherData = null;
  let hourlyData = [];

  async function getWeather() {
      const apiKey = '0dfec882b75e07589ba5fa7b081a4b58';
      if (!city) {
          alert('Please enter a city');
          return;
      }

      const currentWeatherUrl = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}`;
      const forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&appid=${apiKey}`;

      try {
          const weatherResponse = await fetch(currentWeatherUrl);
          const weatherData = await weatherResponse.json();
          displayWeather(weatherData);

          const forecastResponse = await fetch(forecastUrl);
          const forecastData = await forecastResponse.json();
          displayHourlyForecast(forecastData.list);
      } catch (error) {
          console.error("Error:", error);
          alert("Try again");
      }
  }

  function displayWeather(data) {
      if (data.cod === '404') {
          weatherData = { error: data.message };
      } else {
          weatherData = {
              city: data.name,
              temperature: Math.round(data.main.temp - 273.15),
              description: data.weather[0].description,
              iconCode: data.weather[0].icon
          };
      }
  }

  function displayHourlyForecast(hourlyDataList) {
      hourlyData = hourlyDataList.slice(0, 8).map(item => {
          const dateTime = new Date(item.dt * 1000);
          const hour = dateTime.getHours();
          const temperature = Math.round(item.main.temp - 273.15);
          const iconCode = item.weather[0].icon;

          return {
              hour,
              temperature,
              iconUrl: `https://openweathermap.org/img/wn/${iconCode}.png`
          };
      });
  }
</script>

<style>
  body {
      background: #8C52FF;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
  }
  #weather-container {
      background: rgba(255, 255, 255, 0.3);
      max-width: 400px;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
      border: 1px solid rgba(255, 255, 255, 0.1);
      text-align: center;
  }
  h2, label, p {
      color:black;
      margin: 8px 0;
  }
  input {
      width: calc(100% - 16px);
      padding: 8px;
      box-sizing: border-box;
      border-radius: 10px;
      border: 1px solid white;
      margin-top: 20px;
  }
  button {
      background: #debff4;
      color: white;
      padding: 10px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      margin-top: 20px;
      width: 100px;
      font-size: 15px;
  }
  button:hover {
      background: #8b48d7;
  }
  #temp-div p {
      font-size: 60px;
      margin-top: -30px;
  }
  #weather-info {
      font-size: 20px;
  }
  #weather-icon {
      width: 200px;
      height: 200px;
      margin: 0 auto 10px;
      margin-bottom: 0;
  }
  #hourly-forecast {
      margin-top: 50px;
      overflow-x: auto;
      white-space: nowrap;
      display: flex;
      justify-content: space-between;
  }
  .hourly-item {
      flex: 0 0 auto;
      width: 80px;
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-right: 10px;
      color: black;
  }
</style>

<div id="weather-container">
  <h2>Weather App</h2>
  <input type="text" bind:value={city} placeholder="enter city">
  <button on:click={getWeather}>Search</button>
  {#if weatherData}
      {#if weatherData.error}
          <div id="weather-info">
              <p>{weatherData.error}</p>
          </div>
      {:else}
          <img id="weather-icon" alt="Weather Icon" src={`https://openweathermap.org/img/wn/${weatherData.iconCode}@4x.png`}>
          <div id="temp-div">
              <p>{weatherData.temperature} °C</p>
          </div>
          <div id="weather-info">
              <p>{weatherData.city}</p>
              <p>{weatherData.description}</p>
          </div>
      {/if}
  {/if}
  <div id="hourly-forecast">
      {#each hourlyData as item}
          <div class="hourly-item">
              <span>{item.hour}:00</span>
              <img src={item.iconUrl} alt="Hourly Weather Icon">
              <span>{item.temperature}°C</span>
          </div>
      {/each}
  </div>
</div>
