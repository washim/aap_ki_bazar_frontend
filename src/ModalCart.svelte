<script>
    import { cart } from './js/stores';

    const plusItem = (product) => {
        for(let item of $cart) {
			if(item.id === product.id) {
				item.quantity += 1
				$cart = $cart;
				return;
			}
		}
    }

    const minusItem = (product) => {
        for(let item of $cart) {
            if(item.id === product.id) {
                if(product.quantity > 1 ) {
                    product.quantity -= 1
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
                                <span class="ms-auto text-grey-500 fw-500 lh-1 font-xsssss mt-0 w-100 mb-2">{cartitem.weight}</span>
                                <a href="single-product.html" class="text-grey-900 fw-600 font-xssss lh-22 d-block ls-0 mb-2">{cartitem.name}</a>
                                <h6 class="font-xs ls-3 fw-700 text-current float-start mt-1"><span class="font-xsssss text-grey-500">₹</span>{cartitem.price * cartitem.quantity} </h6>
                                <div class="cart-count float-end ">
                                    <div class="number">
                                        <span class="minus" on:click={() => minusItem(cartitem)}>-</span>
                                        <input type="text" class="open-font" value="{cartitem.quantity}">
                                        <span class="plus" on:click={() => plusItem(cartitem)}>+</span>
                                    </div>
                                </div>
                            </div>
                        </div>
                        {/each}
                    </div>              
                    <div class="card w-100 p-4 pt-0 border-0 text-start mt-auto">
                        <!-- <hr /> -->
                        <div class="row">
                            <div class="col-lg-12">
                                <h4 class="text-grey-900 font-xssss fw-600 mb-3 d-flex">Delivery Charges <span class="ms-auto text-grey-500">₹0.00</span></h4>
                                <h4 class="text-grey-900 font-xssss fw-600 mb-2 d-flex">Subtotal <span class="ms-auto text-grey-500">₹{total}</span></h4>
                                <h4 class="text-grey-900 font-xss fw-600 mb-3 d-flex">Total <span class="ms-auto">₹{total}</span></h4>
                            </div>
                        </div>
                        {#if $cart.length > 0}
                        <a href="#" class="w-100 bg-current text-white rounded-6 text-center btn" data-bs-toggle="modal" data-bs-dismiss="modal" data-bs-target="#checkoutproduct" id="checkout2">Checkout</a>
                        {/if}
                    </div>      
                </div>
            </div>
            <div class="checkout-box vh-100" style="display: none;">
                <div class="modal-body vh-100 text-start p-0 d-flex align-items-start flex-column">
                    <div class="card w-100 p-4 pb-0 border-0 text-start">
                        <h4 class="fw-700 font-lg text-grey-900 text-start mb-4 mt-n2 d-block"> Checkout</h4>
                        <div class="row">
                            <div class="col-lg-12">
                                <div class="form-group">
                                    <label for="Name">Name</label>
                                    <input type="text" class="form-control bg-greylight border-0" placeholder="Enter your name" id="Name">
                                </div>
                                <div class="form-group">
                                    <label for="Email">Email</label>
                                    <input type="text" class="form-control bg-greylight border-0" placeholder="Enter your email" id="Email">
                                </div>
                                <div class="form-group">
                                    <label for="Phone">Phone</label>
                                    <input type="text" class="form-control bg-greylight border-0" placeholder="Enter your phone" id="Phone">
                                </div>
                                <div class="form-group">
                                    <label for="Address">Address</label>
                                    <input type="text" class="form-control bg-greylight border-0" placeholder="Enter your address" id="Address">
                                </div>
                            </div>
                            <div class="col-lg-6">
                                <div class="form-group">
                                    <label for="Zip">Zip Code</label>
                                    <input type="text" class="form-control bg-greylight border-0" placeholder="Enter here" id="Zip">
                                </div>
                            </div>
                            <div class="col-lg-6">
                                <div class="form-group">
                                    <label for="City">City</label>
                                    <input type="text" class="form-control bg-greylight border-0" placeholder="Enter here" id="City">
                                </div>
                            </div>
                            <div class="col-lg-12">
                                <div class="form-group">
                                    <label for="Location">Location</label>
                                    <input type="text" class="form-control bg-greylight border-0" placeholder="Enter here location" id="Location">
                                </div>
                                <div class="form-check">
                                    <input class="form-check-input font-xs" type="checkbox" value="Drinks" id="flexCheckStock7">
                                    <label class="form-check-label" for="flexCheckStock7">Save shipping address</label>
                                </div>
                            </div>
                        </div>
                    </div>              
                    <div class="card w-100 p-4 pt-0 border-0 text-start mt-auto">
                        <a href="#" class="w-100 bg-current text-white rounded-6 text-center btn" id="payment">Payment</a>
                    </div>      
                </div>
            </div>

            <div class="payment-box vh-100" style="display: none;">
                <div class="modal-body vh-100 text-start p-0 d-flex align-items-start flex-column">
                    <div class="card w-100 p-4 pb-0 border-0 text-start">
                        <h4 class="fw-700 font-lg text-grey-900 text-start mb-4 mt-n2 d-block"> Payment</h4>
                        <div class="col-lg-12 mt-2">
                            <div class="card bg-white rounded-6 border-0 shadow-xss p-0">
                                <div class="card-body d-flex justify-content-between align-items-end p-4">
                                    <div>
                                        <h4 class="text-grey-600 mb-0 d-flex font-xsss align-items-center justify-content-between mt-0 fw-600">
                                            <img src="https://via.placeholder.com/45x45.png" alt="image" class="float-left me-4">
                                            4321 4432 6565 **** 
                                        </h4>
                                    </div>
                                    <div class="round float-right mb-2">
                                        <input id="radio-1" class="radio-custom" name="radio-group" type="radio" checked="">
                                        <label for="radio-1" class="radio-custom-label m-0"></label>
                                    </div>

                                </div>
                            </div>

                            <div class="card bg-white rounded-6 border-0 shadow-xss mt-3 p-0">
                                <div class="card-body d-flex justify-content-between align-items-end p-4">
                                    <div>
                                        <h4 class="text-grey-600 mb-0 d-flex font-xsss align-items-center justify-content-between mt-0 fw-600">
                                            <img src="https://via.placeholder.com/45x45.png" alt="image" class="float-left me-4">
                                            ***port@gmail.com
                                        </h4>
                                    </div>
                                    <div class="round float-right mb-2">
                                        <input id="radio-2" class="radio-custom" name="radio-group" type="radio">
                                        <label for="radio-2" class="radio-custom-label m-0"></label>
                                    </div>

                                </div>
                            </div>

                            
                            <div class="card bg-white rounded-6 border-0 shadow-xss mt-3 p-0">
                                <div class="card-body d-flex justify-content-between align-items-end p-4">
                                    <div>
                                        <h4 class="text-grey-600 mb-0 d-flex font-xsss align-items-center justify-content-between mt-0 fw-600">
                                            <img src="https://via.placeholder.com/45x45.png" alt="image" class="float-left me-4">
                                            4321 4432 6565 **** 
                                        </h4>
                                    </div>
                                    <div class="round float-right mb-2">
                                        <input id="radio-4" class="radio-custom" name="radio-group" type="radio">
                                        <label for="radio-4" class="radio-custom-label m-0"></label>
                                    </div>

                                </div>
                            </div>
                            <div class="card bg-greylight p-4 mt-3 mb-3 border-0">
                                <h4 class="font-xsssss fw-700 ls-3 mb-4">ADD NEW CARD</h4>

                                <div class="form-group mb-2">
                                    <input type="text" class="form-control bg-white border-0" placeholder="Card Number">
                                </div>
                                <div class="form-group mb-2">
                                    <input type="text" class="form-control bg-white border-0" placeholder="Card Holder Name">
                                </div>
                                <div class="row">
                                    <div class="col-lg-6 pe-2">
                                        <div class="form-group">
                                            <input type="text" class="form-control bg-white border-0" placeholder="Month">
                                        </div>
                                    </div>
                                    <div class="col-lg-6 ps-2">
                                        <div class="form-group">
                                            <input type="text" class="form-control bg-white border-0" placeholder="Year">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-check">
                                    <input class="form-check-input font-xs" type="checkbox" value="Drinks" id="flexCheckStock9">
                                    <label class="form-check-label" for="flexCheckStock9">Save as default</label>
                                </div>
                                <a href="#" class="w-100 bg-black-08 text-white rounded-6 text-center btn mt-2">Save</a>
                                
                            </div>
                            
                        </div>
                    </div>              
                    <div class="card w-100 p-4 pt-0 border-0 text-start mt-auto">
                        <a href="#" class="w-100 bg-current text-white rounded-6 text-center btn" id="checkout3">place Order</a>
                    </div>      
                </div>
            </div>
        </div>
    </div>              
</div> 