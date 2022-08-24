<script>
  import { onMount } from "svelte";
  import { fly } from "svelte/transition";

  let timeSet;
  let state = true;
  $: timeShow = "...";
  $: timeCountdown = "Set the time...";

  const toLocaleConvert = (str_time) => {
    return str_time.toLocaleString("pt-BR", {
      hour12: false,
      hour: "numeric",
      minute: "numeric",
      second: "numeric",
    });
  };

  const timeNow = () => {
    return toLocaleConvert(new Date());
  };

  onMount(() => {
    const interval = setInterval(() => {
      timeShow = timeNow();
    }, 1000);

    return () => {
      clearInterval(interval);
    };
  });

  const setTime = (e) => {
    timeSet = toLocaleConvert(e.target.value);
  };

  const countdown = () => {
    const time = new Date();
    const setTime = Date.parse(timeSet);
    const diff_between = setTime / 1000 - time.getTime() / 1000;
    const hour = Math.floor(diff_between / 3600);
    const minute = Math.floor((diff_between % 3600) / 60);
    const second = Math.floor((diff_between % 3600) % 60);
    const hour_show = hour.toString().padStart(2, "0");
    const minute_show = minute.toString().padStart(2, "0");
    const second_show = second.toString().padStart(2, "0");
    timeCountdown = `${hour_show}:${minute_show}:${second_show}`;
    console.log(diff_between);
    return diff_between;
  };

  const startCountdown = () => {
    const interval = setInterval(() => {
      if (countdown() <= 0) {
        timeCountdown = "TIME'S UP";
        clearInterval(interval);
      }
    }, 1000);
  };
</script>

<main class="main">
  {#if state}
    <div>
      <span>{timeShow}</span>
      <button
        on:click={() => {
          state = false;
        }}>Set countdown</button
      >
    </div>
  {:else}
    <div transition:fly={{ duration: 3000 }}>
      <span>{timeCountdown}</span>
      <div>
        <input
          on:change={(e) => {
            setTime(e);
          }}
          type="datetime-local"
        />
        <button on:click={startCountdown}>Set!</button>
      </div>
    </div>
  {/if}
</main>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Silkscreen&display=swap");

  * {
    font-family: Silkscreen;
  }

  span {
    color: #0e0e16;
    font-size: 9vmin;
    box-sizing: content-box;
  }

  .main {
    width: 100vw;
    height: 100vh;
    background-color: #515160;
    display: flex;
  }

  .main > div {
    margin: auto;
    width: 50%;
    height: 50%;
    background-color: #e2e2ca;
    border-radius: 15px;
    box-shadow: 10px 17px 28px -9px #0e0e16;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
  }

  button,
  input {
    width: auto;
    padding: 1vmin;
    background-color: #91918c;
    color: #0e0e16;
    border-radius: 6px;
    cursor: pointer;
    box-sizing: content-box;
  }

  button:hover {
    background-color: #6c6c72;
  }
</style>