<script>
    import { cart } from './js/stores';

    const plusItem = (product) => {
        for(let item of $cart) {
			if(item.id === product.id) {
				item.quantity += 0.25
				$cart = $cart;
				return;
			}
		}
    }

    const minusItem = (product) => {
        for(let item of $cart) {
            if(item.id === product.id) {
                if(product.quantity > 0.25 ) {
                    product.quantity -= 0.25
                    $cart = $cart
                } else {
                    $cart = $cart.filter((cartItem) => cartItem != product)
                }
                return;
            }
		}
    }

    $: total = $cart.reduce((sum, item) => sum + item.price * item.quantity, 0)
</script>

<div class="modal fade right modal-scrollable" id="cartmodal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered" style="width: 350px;">
        <div class="modal-content theme-dark-bg p-0 border-0 rounded-0">
            <button type="button" class="btn-close z-index-5 bg-grey font-xsssss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-3 me-3" data-bs-dismiss="modal" aria-label="Close"></button>
            <div class="cart-box vh-100" >
                <div class="modal-body vh-100 text-start p-0 d-flex align-items-start flex-column">
                    <div class="card w-100 p-4 pb-0 border-0 text-start">
                        <h4 class="fw-700 font-lg text-grey-900 text-start mb-3 mt-n2 d-block"> Cart</h4>
                        {#each $cart as cartitem }
                        <div class="row mb-3">
                            <div class="col-md-5 col-xs-5"><a href="#" class="d-block text-center"><img src="{cartitem.picture}" alt="product-image" class="w-100 d-inline-block pt-3 pb-3 bg-white rounded-6"/></a></div>
                            <div class="col-md-7 col-xs-7 ps-0">
                                <span class="ms-auto text-grey-500 fw-500 lh-1 font-xsssss mt-0 w-100 mb-2">{cartitem.weight_attributes}</span>
                                <a href="single-product.html" class="text-grey-900 fw-600 font-xssss lh-22 d-block ls-0 mb-2">{cartitem.name}</a>
                                <h6 class="font-xs ls-3 fw-700 text-current float-start mt-1"><span class="font-xsssss text-grey-500">₹</span>{cartitem.price * cartitem.quantity} </h6>
                                <div class="cart-count float-end ">
                                    <div class="number">
                                        <span class="minus" on:click={() => minusItem(cartitem)}>-</span>
                                        <input type="text" class="open-font" value="{cartitem.quantity}" disabled>
                                        <span class="plus" on:click={() => plusItem(cartitem)}>+</span>
                                    </div>
                                </div>
                            </div>
                        </div>
                        {/each}
                    </div>              
                    <div class="card w-100 p-4 pt-0 border-0 text-start mt-auto">
                        <div class="row">
                            <div class="col-lg-12">
                                {#if total > 0 && total <= 130}
                                <h4 class="text-grey-900 font-xssss fw-600 mb-3 d-flex">Delivery Charges <span class="ms-auto text-grey-500">₹10</span></h4>
                                <h4 class="text-grey-900 font-xssss fw-600 mb-2 d-flex">Subtotal <span class="ms-auto text-grey-500">₹{total + 10}</span></h4>
                                <h4 class="text-grey-900 font-xss fw-600 mb-3 d-flex">Total <span class="ms-auto">₹{total +10}</span></h4>
                                {:else}
                                <h4 class="text-grey-900 font-xssss fw-600 mb-2 d-flex">Subtotal <span class="ms-auto text-grey-500">₹{total}</span></h4>
                                <h4 class="text-grey-900 font-xss fw-600 mb-3 d-flex">Total <span class="ms-auto">₹{total}</span></h4>
                                {/if}
                            </div>
                        </div>
                        {#if $cart.length > 0}
                        <a href="#" class="w-100 bg-current text-white rounded-6 text-center btn" data-bs-toggle="modal" data-bs-dismiss="modal" data-bs-target="#checkoutproduct" id="checkout2">Checkout</a>
                        {/if}
                    </div>      
                </div>
            </div>
        </div>
    </div>              
</div> 