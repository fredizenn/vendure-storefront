<script>
  import { goto } from '$app/navigation'
  import { cubicOut } from 'svelte/easing'
  import { tweened } from 'svelte/motion'
  import MagnifyingGlass from './icons/magnifying-glass.svelte'
	import { Input } from 'flowbite-svelte'
	import Icon from '@iconify/svelte'

  let searchTerm = ''

  const submitSearchTerm = () => {
    goto(`/search/${searchTerm}`)
  }

  const width = tweened(0, {
    duration: 200,
    easing: cubicOut,
  })
</script>

<form
  style="transform: scaleX({$width}); transform-origin: 100% 100%;"
  class="form-control"
  on:submit|preventDefault={submitSearchTerm}
>
  <label class="sr-only" for="search_product">Search</label>
  <div class="relative">
    <Input
      class="h-auto input input-primary input-bordered bg-base-200 text-base-content caret-primary"
      placeholder="Search"
      name="search_product"
      bind:value={searchTerm}
      type="text"
    />
  </div>
</form>

<button
  on:click={() => {
    $width === 1 ? width.set(0) : width.set(1)
  }}
  class="btn btn-square btn-ghost"
>
  <Icon icon="bx:search" class="h-6 w-6"/>
</button>
