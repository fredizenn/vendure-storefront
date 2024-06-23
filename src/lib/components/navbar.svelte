<script lang="ts">
	import { browser } from '$app/environment'
	import VendureLogo from '$lib/images/cube-logo-line-icon-nostroke.png'
	import {
		Navbar,
		NavBrand,
		NavLi,
		NavUl,
		NavHamburger,
		Button,
		Input,
	} from 'flowbite-svelte'
	import {
		GQL_GetCollections,
		graphql,
		NavBarStore,
		fragment,
		type NavBarSummary,
	} from '$houdini'
	import { cartOpen } from '$stores/cart'
	import ShoppingCart from './icons/shopping-cart.svelte'
	import Search from './search.svelte'
	import Icon from '@iconify/svelte'

	$: browser && GQL_GetCollections.fetch()
	$: collections =
		$GQL_GetCollections.data?.collections.items.filter(
			item => item?.parent?.name === '__root_collection__'
		) || []

	// query directly in a component as it's relying on LocalStorage, we want to do it only in the browser
	const gql_NavBar: NavBarStore = graphql`
		query NavBar {
			activeOrder {
				...NavBarSummary
			}
		}
	`
	// split with a fragment because we want to use it somewhere else as well
	let activeOrder: NavBarSummary
	$: activeOrder = $gql_NavBar.data?.activeOrder
	$: frag = fragment(
		activeOrder,
		graphql`
			fragment NavBarSummary on Order {
				id
				totalQuantity
			}
		`
	)
</script>

<Navbar>
	<div class="w-full flex items-center justify-between">
    <div class="flex items-center">
      <NavBrand href="/">
        <!-- <img src="/images/flowbite-svelte-icon-logo.svg" class="me-3 h-6 sm:h-9" alt="Flowbite Logo" />
        <span class="self-center whitespace-nowrap text-xl font-semibold dark:text-white">Flowbite</span> -->
        <div class="font-bold">
          Fred's Vendure Storefront

        </div>
      </NavBrand>
      <div class="gap-4 hidden px-2 mx-2 lg:flex items-center">
        {#each collections as collection}
          <a
            href={`/category/${collection.slug}`}
            class="btn text-xs p-1 rounded btn-sm hover:bg-slate-200"
          >
            {collection.name}
          </a>
        {/each}
      </div>
    </div>
    <div class="flex items-center gap-4">
		<Search />
		<div class="relative flex justify-center items-center">
			<span
				class="absolute -right-6 -top-1 flex items-center justify-center text-sm font-bold text-center rounded-2xl h-4 w-6 bg-gray-300"
			>
				{$frag?.totalQuantity || 0}
			</span>
			<button
				on:click={() => {
					$cartOpen = !$cartOpen
				}}
				class="btn btn-square btn-ghost"
			>
				<Icon icon="mdi:cart-outline" class="w-6 h-6" />
			</button>
		</div>
    </div>
		
	</div>

	<!-- <div class="flex md:order-2">
    <Button color="none" data-collapse-toggle="mobile-menu-3" aria-controls="mobile-menu-3" aria-expanded="false" class="md:hidden text-gray-500 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-700 focus:outline-none focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 rounded-lg text-sm p-2.5 me-1">
      <SearchOutline class="w-5 h-5" />
    </Button>
    <div class="hidden relative md:block">
      <div class="flex absolute inset-y-0 start-0 items-center ps-3 pointer-events-none">
        <SearchOutline class="w-4 h-4" />
      </div>
      <Input id="search-navbar" class="ps-10" placeholder="Search..." />
    </div>
    <NavHamburger />
  </div> -->
	<!-- <NavUl>
    <NavLi href="/" active={true}>Home</NavLi>
    <NavLi href="/about">About</NavLi>
    <NavLi href="/docs/components/navbar">Navbar</NavLi>
  </NavUl> -->
</Navbar>

<nav class="navbar shadow-lg bg-white text-black sticky top-0 z-30">
</nav>
