<script>
  import axios from "axios";
  import Loading from "./loading.svelte";

  const appid_openweather = process.env.API_OPENWEATHER;
  const apikey_heremaps = process.env.API_KEY_HEREMAPS;
  var loading = false;
  var city = "";
  var tempcity = "";
  var temp = "";
  var humidity = "";
  var description = "";
  var mapurl = "";
  var zoomlevel = 7;
  var incomeData = null;
  var error = "";

  const getData = () => {
    loading = true;
    error = "";
    console.log(city);

    axios
      .get(
        `https://api.openweathermap.org/data/2.5/weather?q=${city}&APPID=${appid_openweather}&units=metric`
      )
      .then((data) => {
        tempcity = city;
        // console.log(data.data)
        incomeData = data.data;
        temp = incomeData.main.temp;
        humidity = incomeData.main.humidity;
        description = incomeData.weather[0].description;
        mapurl = `https://image.maps.ls.hereapi.com/mia/1.6/mapview?apiKey=${apikey_heremaps}&c=${incomeData.coord.lat},${incomeData.coord.lon}&t=0&z=${zoomlevel}&w=700&h=700`;
        loading = false;
        city = "";
      })
      .catch((err) => {
        console.log(err.response);
        error = "City doesn't exist.";
        loading = false;
        incomeData = null;
        city = "";
      });
  };
</script>

<div class="maindiv">
  <form on:submit|preventDefault={getData}>
    <input type="text" bind:value={city} placeholder="Enter your city" />
  </form>

  {#if error}
    <h4>{error}</h4>
  {/if}

  {#if loading}
    <Loading />
  {/if}

  {#if incomeData !== null}
    <div class="data">
      <h4>Weather in <span class="cap">{tempcity}</span></h4>
      <h6>Tempurature: {temp}&deg</h6>
      <h6>Humidity: {humidity}%</h6>
      <h6>Weather: {description}</h6>
    </div>
    <div class="row">
      <div class="seven columns"><img src={mapurl} alt="mapImageView" /></div>
    </div>
    <div class="u-cf" />
  {/if}
</div>
