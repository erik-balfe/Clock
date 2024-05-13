<script>
  import { onMount } from 'svelte';

  let hours = [];
  let minutes = [];
  let seconds = [];

  let hourRotation = 0;
  let minuteRotation = 0;
  let secondRotation = 0;

  function updateTime() {
    const now = new Date();
    const hour = now.getHours();
    const minute = now.getMinutes();
    const second = now.getSeconds();

    hours = new Array(4).fill(hour);
    minutes = new Array(7).fill(minute);
    seconds = new Array(8).fill(second);

    let hourNormal = hour % 12;
    hourRotation = -90 + (hourNormal * 30) + (minute / 60 * 30);
    minuteRotation = -90 + (minute * 6);
    secondRotation = -90 + (second * 6);
  }

  onMount(() => {
    updateTime();
    const interval = setInterval(updateTime, 1000);
    return () => clearInterval(interval);
  });

  function calculateOpacity(index, total) {
    return (index / (total - 1)) * 1;
  }
  export const prerender = true;
</script>

<div class="container">
  <svg viewBox="-50 -50 100 100" width="300" height="300" class="container">
    <circle cx="0" cy="0" r="48" fill="none" stroke="black" />  
    {#each Array(12) as _, i}
      <line x1="0" y1="-45" x2="0" y2="-48" stroke="black" transform="rotate({i * 30})" />
    {/each}
    {#each Array(12) as _, i}
      <g transform={`rotate(${i * 30}) translate(0, -40)`}>
        <text text-anchor="middle" font-size="8px" font-family="monospace" transform={`rotate(-${i * 30}) translate(0, 3)`}>{i === 0 ? 12 : i}</text>
      </g>
    {/each}
  </svg>

  <div class="arrow hour" style="transform: rotate({hourRotation}deg)">
    {#each hours as digit, i}
      <div class="arrowDigit" style={`transform: rotate(${-hourRotation}deg); opacity: ${calculateOpacity(i, hours.length)}`}>{String(digit).padStart(2, '0')}</div>
    {/each}
  </div>
  <div class="arrow minute" style="transform: rotate({minuteRotation}deg)">
    {#each minutes as digit, i}
      <div class="arrowDigit" style={`transform: rotate(${-minuteRotation}deg); opacity: ${calculateOpacity(i, minutes.length)}`}>{String(digit).padStart(2, '0')}</div>
    {/each}
  </div>
  <div class="arrow second" style="transform: rotate({secondRotation}deg)">
    {#each seconds as digit, i}
      <div class="arrowDigit" style={`transform: rotate(${-secondRotation}deg); opacity: ${calculateOpacity(i, seconds.length)}`}>{String(digit).padStart(2, '0')}</div>
    {/each}
  </div>
</div>

<style>
  .arrow {
    position: absolute;
    transform-origin: left;
    top: calc(50% - 7px);
    left: 50%;
    display: flex;
    gap: 5px;
    height: 14px;
  }
  .arrowDigit {
    width: 10px;
  }
  .second {
    color: #e63946; /* Bold Red */
    position: absolute;
    width: 46%;
    font-size: 9px;
  }
  .minute {
    color: #457b9d; /* Muted Blue */
    font-size: 13px;
  }
  .hour {
    color: #1d3557; /* Dark Blue */
  }
  .container {
    position: relative;
    background: #f1faee; /* Very Light Green - Mint Cream */
    border: 2px solid #a8dadc; /* Light Blue-Green */
    width: 300px;
    height: 300px;
  }
  </style>