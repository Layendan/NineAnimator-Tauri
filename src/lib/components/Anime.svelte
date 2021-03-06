<script lang="ts">
  // Import required packages
  import loadingFailure from "$lib/components/assets/loading_failure.jpeg";
  import { fade } from "svelte/transition";
  import DOMPurify from "dompurify";
  import type { Episode } from "$lib/model/anime";

  // Export component definitions
  export let id: number | null = null;
  export let name: string = "Loading";
  export let thumbnail: string = loadingFailure;
  export let description: string = "";
  export let episodes: Episode[] | null = null;
  export let isNSFW: boolean = false;

  let loading: boolean = true;

  // Check if there is data to store before looking at the length or else returns null error
  const DATA_NAME = name + "-episodes";
  if (
    episodes &&
    episodes.length > 0 &&
    !window.sessionStorage.getItem(DATA_NAME)
  ) {
    window.sessionStorage.setItem(DATA_NAME, JSON.stringify(episodes));
  }
</script>

<body transition:fade>
  <a href="/{id}" class:unselectable={id === null} on:click>
    <span class="holder">
      {#key loading}
        <img
          src={thumbnail}
          loading="lazy"
          alt={name}
          class:hide={loading}
          on:error={() => (thumbnail = loadingFailure)}
          on:load={() => (loading = false)}
          transition:fade
        />
      {/key}
      {#if loading}
        <img src={loadingFailure} alt={name} in:fade />
      {/if}
      <div class="info">
        <div class="text">
          <h1>
            {name ?? "Loading"}
            {#if isNSFW}
              <span class="nsfw">18+</span>
            {/if}
          </h1>
          <p>
            {@html DOMPurify.sanitize(description, {
              USE_PROFILES: { html: true },
            })}
          </p>
        </div>
      </div>
    </span>
  </a>
</body>

<style>
  body {
    width: auto;
    display: inline-flex;
    vertical-align: top;
    margin-left: 15px;
    margin-right: 15px;

    /* I'm just gonna use overflow-y because line clamping slows down the loading of the page by a few seconds and that's not fun for the user */
    overflow-y: hidden;
    text-overflow: ellipsis;
  }

  a:focus > .holder {
    border-color: var(--accent-color);
  }

  .unselectable {
    pointer-events: none;
  }

  body a {
    text-decoration: none;
  }

  body img {
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    width: 178px;
    height: 252px;
    border-radius: 10px 0 0 10px;
    border-right: 2px var(--tertiary-color) solid;

    transition: filter 0.2s ease-in-out;
  }

  body a:hover img {
    filter: brightness(115%);
  }
  body a:hover .text {
    transform: translateY(-1px);
  }
  body a:hover .info {
    background-color: var(--tertiary-color);
  }

  body a:hover .holder {
    border-color: var(--pure-white);
  }

  a {
    text-decoration: none;
    color: var(--text-color);
  }

  h1 {
    display: inline-flex;
    font-size: 1em;
    margin: 0;
    margin-left: 1em;
    margin-right: 1em;
    padding-bottom: 0;
    height: auto;
    width: 85%;
    word-wrap: break-word;
    white-space: normal;
    hyphens: auto;
    justify-content: space-between;
  }

  h1 .nsfw {
    color: var(--danger-color);
    padding-top: 0.1em;
    font-weight: bold;
    font-size: smaller;
    margin-left: 0.5em;
  }

  p {
    font-size: 1em;
    margin: 0;
    margin-left: 1em;
    margin-top: 1em;
    height: auto;
    word-wrap: break-word;
    white-space: normal;
    hyphens: auto;
    font-weight: 100;
    padding-right: 1em;
  }

  .holder {
    max-width: 450px;
    width: 400px;
    min-width: 160px;
    display: flex;
    border-radius: 12px;
    border-color: var(--tertiary-color);
    border-style: solid;
    border-width: 2px;
    background-color: var(--secondary-color);

    transition: border-color 0.2s ease-in-out;
  }

  .info {
    width: 100%;
    height: 100%;
    border-radius: 0 10px 10px 0;
    padding-top: 10px;
    padding-bottom: 20px;
    height: 222px;

    transition: background-color 0.2s ease-in-out;
  }

  .text {
    transition: transform 0.2s ease-in-out;
    color: var(--text-color);
    margin-bottom: 1em;
  }

  .hide {
    display: none;
  }
</style>
