<script>
  import { onMount} from 'svelte';
  import '@fortawesome/fontawesome-free/css/all.min.css';

  let pages = [];
  let currentPageIndex = 0;
  let title = 'Hello World';
  let note = 'This is a note';

  onMount(() => {
    const savedPages = localStorage.getItem("pages");
    if (savedPages) {
      pages = JSON.parse(savedPages);
      title = pages[currentPageIndex];
      note = localStorage.getItem(title);
    } else {
      addPage();
    }
  })

  function saveNote() {
    const storedPageName = pages[currentPageIndex];
    if (storedPageName !== title) {
      localStorage.removeItem(storedPageName);
      pages[currentPageIndex] = title;
    }
    localStorage.setItem(title, note);
    localStorage.setItem("pages", JSON.stringify(pages));
  }

  function addPage() {
    pages.push("New Page");
    selectPage(pages.length ? pages.length - 1 : 0);
  }

  function selectPage(index) {
    currentPageIndex = index;
    title = pages[currentPageIndex];
    note = localStorage.getItem(title);
  }

  // Function to delete a page
  function deletePage(index) {
    const deletedPage = pages[index];
    pages = pages.filter((_, i) => i !== index);
    localStorage.removeItem(deletedPage);
    if (index === currentPageIndex) {
      currentPageIndex = Math.min(currentPageIndex, pages.length - 1);
      if (pages.length > 0) {
        selectPage(currentPageIndex);
      } else {
        title = '';
        note = '';
      }
    }
  }
  
</script>

<aside class="fixed top-0 left-0 z-40 w-60 h-screen">
  <div class="bg-light-gray overflow-y-auto py-5 px-3 h-full border-r border-gray-200">
    <ul>
      {#each pages as page, index}
        <li class="flex align-center">
            <button on:click={()=>selectPage(index)} class="{index == currentPageIndex ? 'bg-dark-gray' : ''} py-2 px-3 text-gray-900 rounded-lg m-1">{page}</button>
            <button on:click={() => deletePage(index)}><i class="fas fa-trash-alt text-red-600"></i></button>
        </li>
      {/each}
      <li class="text-center"><button on:click={addPage} class="font-medum">+ Add page</button></li>
    </ul>
  </div>
</aside>

<main class="p-4 ml-60 h-auto">
  <div class="grid grid-cols-2 items-center mb-3">
    <h1 class="text=3xl font-bold" contenteditable bind:textContent={title}></h1>
    <button class="ml-auto bg-gray-800 text-white px-5 py-2.5 rounded-lg font-medum text-sm mt-3 hover:bg-gray-900" on:click={saveNote}>Save</button>
  </div>
  <hr/>
  <!-- <input class="block w-full bg-gray-50 border border-gray-300 rounded-lg text-gray-900 p-2.5" bind:value={title} type='text'> -->
  <textarea class="mt-3 block w-full bg-gray-50 border border-gray-300 rounded-lg text-gray-900 p-2.5" bind:value={note}></textarea>
  
</main>

<style>
.bg-light-gray {
  background-color: #FBFBFB;
}

.bg-dark-gray {
  background-color: #EFEFEF;
}
</style>