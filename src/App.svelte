<script>
    import url from './js/url';
    import Products from './Products.svelte';
    import TopHeader from './TopHeader.svelte';
    import LowerHeader from './LowerHeader.svelte';
    import HomeBanner from './HomeBanner.svelte';
    import Highlights from './Highlights.svelte';
    import Footer from './Footer.svelte';
    import ModalCheckout from './ModalCheckout.svelte';
    import ModalPages from './ModalPages.svelte';
    import ModalCategory from './ModalCategory.svelte';
    import ModalCart from './ModalCart.svelte';
    import Videos from './videos.svelte';
</script>

<main>
    <div class="main-wrapper bg-lightgrey">
        <TopHeader/>
        <LowerHeader/>
        {#if $url.hash === '' || $url.hash === '#/'}
            <HomeBanner/>
            <Highlights/>
            <Products/>
        {:else if $url.hash === '#/videos'}
            <Videos/>
        {:else}
            <div class="main-wrap pt-4">
                <div class="container">
                    <div class="row">
                        <h3>404</h3>
                        <p>Page not found.</p>
                    </div>
                </div>
            </div>
        {/if}
        <Footer/>
    </div>
    <ModalCheckout/>
    <ModalPages/>
    <ModalCategory/>
    <ModalCart/>
</main>