<script lang="ts">
  
  import Header from "./sections/header.svx";

  import Navbar from "./lib/Navbar.svelte";
  import pages from "./pages"

  let showAll = false;

  let activeItem = pages[0][1];
  function onHashChange() {
    showAll = location.hash == "#all";
    activeItem = pages[Number(location.hash.slice(1))]?.[1] || pages[0][1];
  }

  onHashChange();
  $: !showAll &&
    (location.hash = String(
      pages.findIndex(([_, elem]) => elem === activeItem)
    ));

  let containerElem: HTMLElement;
  let componentRef;
  let firstRun = true;
  $: if (componentRef && containerElem) {
    containerElem.querySelectorAll("blockquote").forEach((elem) => {
      if (elem.textContent.startsWith("$> ")) {
        elem.style.userSelect = "none";
        elem.style.setProperty("--leftLineColour", "#21b0d7");
      }
      if (elem.textContent.startsWith("splunk> ")) {
        elem.style.userSelect = "none";
        elem.style.setProperty("--leftLineColour", "#5cbf5c");
      }
    });

    if (!showAll) {
      if (firstRun) {
        firstRun = false;
      } else {
        setTimeout(() => window.scrollTo(0, containerElem.offsetTop - 50), 0);
      }
    }
  }
</script>

<svelte:window on:hashchange={onHashChange} />
<Header />
<hr />

{#if showAll}
  {#each pages as [_, component]}
    <svelte:component this={component} />
  {/each}
{:else}
  <Navbar items={pages} bind:activeItem />

  <main bind:this={containerElem}>
    <svelte:component this={activeItem} bind:this={componentRef} />
  </main>
{/if}

<style>
  main {
    min-height: 100vh;
  }
</style>
