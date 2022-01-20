<script>
    import { onMount } from 'svelte';
    import { cart } from './js/stores';

    let products = [];

    onMount(async () => {
		const res = await fetch(`https://ecommerce.nbsols.com/api/products/`);
		products = await res.json();
	});

    const addToCart = (product) => {
        for(let item of $cart) {
            if(item.id === product.id) {
                product.quantity += 1;
                $cart = $cart;
                return;
            }
        }
        $cart = [...$cart, product]
    }
</script>

<div class="main-wrap pt-4">
    <div class="container">
        <div class="row">
            <div class="col-lg-3 pe-lg-0 d-none d-lg-block">
                <div class="card w-100 border-0 shadow-none mb-3 ovh rounded-6 hover-zoom-image">
                    <img src="/images/side-banner-4.jpg" alt="" class="w-100">
                    <div class="p-4 posa bottom-0 w-100">
                        <span class="fw-700 ls-3 text-white bg-current ps-2 pe-2 lh-24 rounded-6 d-inline-block font-xsssss">30% OFF</span>
                        <h4 class="font-xs fw-700 lh-28 text-grey-900 mb-3 mt-3 ls-0">High Quality <br> Products</h4>
                        <a href="#" class="fw-700 ls-3 text-grey-900 font-xsssss">SHOP NOW</a>
                    </div>  
                </div>
            </div>
            <div class="col-lg-9">
                <div class="row">
                    <div class="col-lg-12">
                        <h4 class="fw-700 font-xs mb-3">Deal of the day</h4>
                    </div>
                </div>
                <div class="row rounded-6 m-0 bg-white">
                    {#each products as product}
                    <div class="col-lg-3 col-sm-3 col-xs-6 p-3 border-none-xs border-end rounded-0 posr">
                        <a href="#" class="d-block text-center">
                            <img src="https://ecommerce.nbsols.com/media/{product.picture}" alt="product-image" class="w-100 mt-3 mb-3 d-inline-block p-2 pt-0" style="width: 171px; height:138px;">
                        </a>
                        <div class="clearfix"></div>
                        <h2 class="mt-2"><a href="single-product.html" class="text-grey-700 fw-600 font-xsss lh-22 d-block ls-0">{product.name}</a></h2>
                        <h6 class="font-xss ls-3 fw-700 text-current d-flex">
                            <span class="font-xsssss text-grey-500">₹</span>{product.price} <span class="ms-auto text-grey-500 fw-500 mt-1 font-xsssss">{product.weight_attributes}</span>
                        </h6>
                        <button type="button" class="btn btn-sm btn-success" data-bs-toggle="modal" data-bs-target="#cartmodal" on:click={() => addToCart(product)}>Add to cart</button>
                    </div>
                    {/each}
                </div>
                <div class="row">
                    <div class="col-lg-12 mt-3 mb-3">
                        <div class="card border-0 banner-wrap bg-image-cover bg-image-center" style="background-image: url(/images/bg-grocery-2.jpg);">
                            <div class="slide-content style4 text-center w-100">
                                <span class="text-current">All natural products</span>
                                <h2 class="text-grey-900"><b class="d-block">SUMMER DISCOUNT </b>of the week</h2>    
                                <div class="clearfix"></div>
                                <a href="#" class="btn-lg rounded-25 btn bg-current">SHOP NOW</a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>