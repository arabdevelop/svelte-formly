<script>
  import { createEventDispatcher, onMount } from "svelte";
  import clsx from "clsx";
  // Declar variables.
  export let id = false;
  export let name = "";
  export let loadItemes = [
    {
      id: 1,
      title: "item 1"
    },
    {
      id: 2,
      title: "item 2"
    },
    {
      id: 3,
      title: "item 3"
    }
  ];
  let items = loadItemes;
  export let disabled = false;
  export let multiple = false;
  let hideListItems = true;
  let itemsSelected = [];
  let input = undefined;
  const dispatch = createEventDispatcher();

  // Change value.
  function onChangeValue(event) {
    dispatch("changeValue", {
      name: name,
      value: event.target.value
    });
  }

  // Select item.
  const onSelectItem = item => {
    let newValue;
    hideListItems = true;
    if (multiple) {
      itemsSelected = [...itemsSelected, item];
      newValue = itemsSelected;
      // Delete tag from list.
      let filterItems;
      itemsSelected.forEach(s => {
        items = loadItemes.filter(i => i.id != item.id);
      });
      console.log("items", items);
      // items = loadItemes;
    } else {
      itemsSelected = [item];
      newValue = item;
    }
    // Update value field
    dispatch("changeValue", {
      name: name,
      value: newValue
    });
    // Add disptach for custom function user.
    dispatch("onSelectItem", item);
  };

  // Delete tag from list selected.
  const deleteTag = item => {
    // Delete tag from list selected,
    itemsSelected = itemsSelected.filter(i => i.id != item.id);
    // Add tag deleted to list item.
    items = [...items, item];
  };

  // Clear all tags.
  const clearAll = () => {
    itemsSelected = [];
    hideListItems = true;
    items = loadItemes;
  };

  // Filter items.
  const onFilter = e => {
    const keyword = e.target.value;
    const filtered = items.filter(entry => {
      return Object.values(entry).some(
        val => typeof val === "string" && val.includes(keyword)
      );
    });
    if (filtered.length > 0) {
      items = filtered;
    }
  };
</script>

<style>
  .select-container {
    border: solid 1px #dddddd;
    border-radius: 4px;
    height: auto;
    position: relative;
    display: flex;
    flex-wrap: wrap;
  }
  .select-container input {
    border: none;
    padding: 0 15px;
    height: 40px;
    width: 100%;
    /* position: absolute; */
    box-sizing: border-box;
  }
  .items-container {
    box-shadow: 0 2px 3px 0 rgba(44, 62, 80, 0.24);
    background: #ffffff;
    border-radius: 0 0 5px 5px;
    overflow-y: auto;
    position: absolute;
    top: 0;
    z-index: 2;
    width: 100%;
  }
  .items-container .item,
  .item-selected {
    height: 40px;
    line-height: 40px;
    padding: 0 20px;
    overflow: hidden;
  }
  .items-container .item:first-child,
  .items-container .item:hover {
    background-color: #fbd5c8;
    cursor: pointer;
  }
  .item-selected,
  .clear-all {
    position: absolute;
    z-index: 2;
  }
  .item-selected {
    display: flex;
    flex-wrap: wrap;
  }
  .clear-all {
    right: 20px;
    width: 20px;
    height: 40px;
    cursor: pointer;
  }
  .tag {
    background-color: #ddd;
    border-radius: 15px;
    height: 25px;
    line-height: 25px;
    margin: 8px 5px;
    padding: 0 30px 0 10px;
    position: relative;
    display: flex;
    flex-wrap: wrap;
  }
  .tag .clear {
    box-shadow: 0 2px 3px 0 rgba(44, 62, 80, 0.24);
    border-radius: 50%;
    width: 15px;
    height: 15px;
    padding: 2px;
    position: absolute;
    right: 5px;
    top: 3px;
    background-color: #ff3e00;
  }
  .tag .clear:hover {
    background-color: #fbd5c8;
  }
  .tag .clear svg {
    fill: white;
    vertical-align: top;
  }
  input:focus {
    outline: none;
  }
</style>

<div class="select-container">
  <!-- Selected item and clear button -->
  {#if itemsSelected.length > 0}
    {#each itemsSelected as itemSelected}
      <div class="item-selected {multiple ? 'tag' : ''}">
        <span>{itemSelected.title}</span>
        {#if multiple}
          <div class="clear" on:click={() => deleteTag(itemSelected)}>
            <svg
              width="100%"
              height="100%"
              viewBox="-2 -2 50 50"
              focusable="false">
              <path
                d="M34.923,37.251L24,26.328L13.077,37.251L9.436,33.61l10.923-10.923L9.436,11.765l3.641-3.641L24,19.047L34.923,8.124
                l3.641,3.641L27.641,22.688L38.564,33.61L34.923,37.251z" />
            </svg>
          </div>
        {/if}
      </div>
    {/each}
    <div
      class="clear-all"
      on:click={() => {
        clearAll();
      }}>
      <svg
        width="100%"
        height="100%"
        viewBox="-2 -2 50 50"
        focusable="false"
        role="presentation"
        class="svelte-e3bo9s">
        <path
          fill="currentColor"
          d="M34.923,37.251L24,26.328L13.077,37.251L9.436,33.61l10.923-10.923L9.436,11.765l3.641-3.641L24,19.047L34.923,8.124
          l3.641,3.641L27.641,22.688L38.564,33.61L34.923,37.251z" />
      </svg>
    </div>
  {/if}
  <!-- Input to autocomplete -->
  <input
    type="text"
    spellcheck="false"
    autocorrect="off"
    autocomplete="off"
    {id}
    {disabled}
    bind:this={input}
    on:keyup={onFilter}
    on:focus={() => (hideListItems = false)} />
  <!-- List items -->
  {#if !hideListItems}
    <div style="position: relative; width: 100%;">
      <div class="items-container">
        {#each items as item}
          <div
            class="item"
            on:click={() => {
              onSelectItem(item);
            }}>
            <span>{item.title}</span>
          </div>
        {/each}
      </div>
    </div>
  {/if}

</div>