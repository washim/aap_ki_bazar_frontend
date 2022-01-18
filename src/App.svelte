<script>
    import { Router, Route, Link } from "svelte-navigator";
    import { onMount } from 'svelte';
    import Carousel from 'svelte-carousel'
    
    let cart = [];
    let products = [];
    let customer = localStorage.getItem("shippingAddress") ? JSON.parse(localStorage.getItem("shippingAddress")) : {
        'name': '',
        'mobile': '',
        'address': '',
        'ward_no': '',
        'pincode': ''
    };
    let itemWeight = {id: 0, weight: ""};
    let orderCompleted = false;
    let orderMessage = '';
    
    onMount(async () => {
		const res = await fetch(`https://ecommerce.nbsols.com/api/products/`);
		products = await res.json();
	});

    const addToCart = (product) => {
        if (product.id === itemWeight.id) {
            for(let item of cart) {
                if(item.id === product.id) {
                    product.quantity += 1;
                    product.weight = itemWeight.weight;
                    cart = cart;
                    return;
                }
            }
            product.weight = itemWeight.weight;
            cart = [...cart, product]
        } else {
            alert('Choose product weight');
        }
    }

    const plusItem = (product) => {
        for(let item of cart) {
			if(item.id === product.id) {
				item.quantity += 1
				cart = cart;
				return;
			}
		}
    }

    const minusItem = (product) => {
        for(let item of cart) {
            if(item.id === product.id) {
                if(product.quantity > 1 ) {
                    product.quantity -= 1
                    cart = cart
                } else {
                    cart = cart.filter((cartItem) => cartItem != product)
                }
                return;
            }
		}
    }

    async function doSubmitOrder() {
        if (cart.length > 0) {
            const customer_data = JSON.stringify(customer);
            const create_shipping = await fetch('https://ecommerce.nbsols.com/api/shipping/', {
                method: 'POST',
                headers: {'Authorization': 'Basic YWRtaW46UGFzc3dvcmRANzg2Iw==', 'Content-Type': 'application/json'},
                body: customer_data
            });
            const shipping = await create_shipping.json()
            localStorage.setItem("shippingAddress", customer_data);
            if (shipping.id) {
                const create_invoice = await fetch('https://ecommerce.nbsols.com/api/invoices/', {
                    method: 'POST',
                    headers: {'Authorization': 'Basic YWRtaW46UGFzc3dvcmRANzg2Iw==', 'Content-Type': 'application/json'},
                    body: JSON.stringify({"status": "processed", "status_message": "Processed","shipping": shipping.id})
                });
                const invoice = await create_invoice.json();
                if (invoice.id) {
                    for(let product of cart) {
                        const create_order = await fetch('https://ecommerce.nbsols.com/api/orders/', {
                            method: 'POST',
                            headers: {'Authorization': 'Basic YWRtaW46UGFzc3dvcmRANzg2Iw==', 'Content-Type': 'application/json'},
                            body: JSON.stringify({"quantity": product.quantity, "weight": product.weight, "invoice": invoice.id, "product": product.id})
                        });
                    }
                }
                orderMessage = 'Your order successfully placed.';
                orderCompleted = true;
                cart = [];
                itemWeight = {id: 0, weight: ""};
                setTimeout(() => {orderCompleted = false; orderMessage = '';}, 5000);
            }
        } else {
            orderMessage = 'Cart become empty. Choose products again for new order.';
        }
    }

    $: total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0)
</script>

<Router>
    <main>
        <!-- main wrapper  -->
        <div class="main-wrapper bg-lightgrey">
            <!-- HEADER WRAPPER -->
            <div class="header-menu-mob pt-2 pb-2 shadow-xss position-fixed w-100 z-index-5 bg-white d-none d-block-md">
                <div class="container">
                    <div class="row">
                        <div class="col text-start"><button class="navbar-toggler border-0" type="button" data-bs-toggle="modal" data-bs-target="#menumodal"><span class="navbar-toggler-icon"></span></button></div>
                        <div class="col text-center"><a href="index.html"><div class="logo w-90">App ki Bazar</div></a></div>
                        <div class="col text-end"><a href="index.html" class="nav-icon mt-1 d-inline-block" data-bs-toggle="modal" data-bs-target="#cartmodal"><i class="feather-shopping-bag text-grey-500 font-xl"></i></a></div>
                    </div>
                </div>
            </div>
            <div class="upper-header pt-2 pb-2 bg-green d-none d-lg-block">
                <div class="container">
                    <div class="row">
                        <div class="col-lg-6">
                            <ul class="nav">
                                <li class="nav-item"><Link to="about" class="ps-0">About</Link></li>
                            </ul>
                        </div>
                        <div class="col-lg-6 text-end">
                            <ul class="navbar-nav float-end">
                                <li class="nav-item">
                                    <a class="nav-link" href="#">Terms and Conditions</a>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
            <div class="header-wrapper bg-invert pt-4 pb-4 z-index-5 ovh bg-green d-none d-lg-block">
                <div class="container">
                    <div class="row">
                        <div class="col-lg-12 d-flex">
                            <a href="#"><div class="logo">App ki Bazar</div></a>
                            <div class="header-search ms-auto me-2 d-flex">
                                <a href="#" class="nav-icon" data-bs-toggle="modal" data-bs-target="#cartmodal"><i class="feather-shopping-bag text-grey-500"></i> <span style="color: white; position:relative; top:-5px;">Cart Total: ₹{total}</span></a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="lower-header pt-0 pb-0 bg-invert shadow-xss z-index-1 bg-deepgreen d-none d-lg-block">
                <div class="container">
                    <div class="row">
                        <div class="col-lg-3 pe-0">
                            <div class="dropdown dropdown-right border-0">
                                <button class="btn border-0 rounded-0 dropdown-toggle dropdown-toggle-white bg-warning ps-3" type="button" data-bs-toggle="modal" data-bs-target="#categorymodal"><i class="feather-align-left"></i> All Categories</button>
                                <ul class="dropdown-menu w-100 border-0 mt-3 shadow-xss" >
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Biscuits &amp; Snacks </a></li>
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Breads &amp; Bakery </a></li>
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Breakfast &amp; Dairy <span class="alert-success text-success">Offer</span></a></li>
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Frozen Foods </a></li>
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Fruits &amp; Vegetables </a></li>
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Grocery &amp; Staples </a></li>
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Household Needs </a></li>
                                    <li><a class="dropdown-item dropdown-toggle" href="#">Meats &amp; Seafood </a></li>
                                </ul>
                            </div>
                        </div>
                        <div class="col-lg-9">
                            <div class="navbar navbar-expand-lg p-0">
                                <div class="navbar-collapse collapse show" id="main_nav" style="">
                                    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#main_nav" aria-expanded="true" aria-label="Toggle navigation"><span class="navbar-toggler-icon"></span></button>
                                    <ul class="navbar-nav">
                                        <li class="nav-item active">
                                            <Link to="/" class="nav-link">Home</Link>
                                        </li>
                                        <li class="nav-item active">
                                            <Link to="about" class="nav-link">About Us</Link>
                                        </li>
                                        <li class="nav-item active">
                                            <a class="nav-link" href="#">Terms and Conditions</a>
                                        </li>
                                    </ul>
                                    <ul class="navbar-nav ms-auto">
                                        <li class="nav-item text-grey-200 fw-500 font-xssss">Need help? Call Us : <a href="tel:07908842100" class="fw-700 text-white" >+917908842100</a></li>
                                    </ul>
                                </div>
                            </div>
                        </div>        
                    </div>
                </div>
            </div>
            <!-- HEADER WRAPPER -->
            <Route path="/">
                <div class="banner-wrapper pt-4 pb-3 md-mt-6">
                    <div class="container">
                        <div class="row">
                            <div class="col-lg-12">
                                <div class="banner-wrap">
                                    <Carousel let:showPrevPage let:showNextPage autoplay autoplayDuration={5000} autoplayProgressVisible pauseOnFocus>
                                    <div slot="prev" on:click={showPrevPage} class="custom-arrow custom-arrow-prev badge badge-pill badge-primary">
                                        <i class="feather-chevron-left"></i>
                                    </div>
                                    <div class="item bg-image-cover ovh style1 d-flex justify-content-start" style="background-image: url(/images/banner-slider-2.jpg);">
                                        <div class="slide-content text-left w-50 ps-lg-5">
                                            <span class="text-grey-700">All natural products</span>
                                            <h2 class="text-grey-900"><b class="d-block">Special Offer </b>of the week</h2>    
                                            <p class="text-grey-600">App Ki Bazar food is food produced by methods that comply with the standard of farming.</p>
                                            <div class="clearfix"></div>
                                            <a href="#" class="btn-lg rounded-25 btn bg-current">SHOP NOW</a>
                                        </div>
                                    </div>
                                    <div class="item bg-image-cover ovh style1 d-flex justify-content-center" style="background-image: url(/images/banner-slider-5.jpg);">
                                        <div class="slide-content text-center w-50">
                                            <span class="text-grey-700">All natural products</span>
                                            <h2 class="text-grey-900"><b>Healty Food Pure</b> App Ki Bazar</h2>    
                                            <p class="text-grey-600">App Ki Bazar food is food produced by methods that comply with the standard of farming.</p>
                                            <div class="clearfix"></div>
                                            <a href="#" class="btn-lg rounded-25 btn bg-current">SHOP NOW</a>
                                        </div>
                                    </div>
                                    <div class="item bg-image-cover ovh style1 d-flex justify-content-center" style="background-image: url(/images/banner-slider-1.jpg);">
                                        <div class="slide-content text-center w-50">
                                            <span class="text-grey-700">All natural products</span>
                                            <h2 class="text-grey-900"><b class="d-block">Summer Discount</b> App Ki Bazar Home Delivery</h2>    
                                            <p class="text-grey-600">App Ki Bazar food is food produced by methods that comply with the standard of farming.</p>
                                            <div class="clearfix"></div>
                                            <a href="#" class="btn-lg rounded-25 btn bg-current">SHOP NOW</a>
                                        </div>
                                    </div>
                                    <div slot="next" on:click={showNextPage} class="custom-arrow custom-arrow-next badge badge-pill badge-primary">
                                        <i class="feather-chevron-right"></i>
                                    </div>
                                    </Carousel>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- CATEGORY WRAPPER -->
                <div class="content-wrap">
                    <div class="container">
                        <div class="row m-0">
                            <div class="col-lg-12 bg-white rounded-6 p-4 pb-3">
                                <div class="row">
                                    <div class="col-lg-3 col-md-6 xs-mb-4 md-mb-3 text-start">
                                        <i class="ti-shopping-cart text-grey-900 display1-size float-start me-3"></i>
                                        <h4 class="mt-0 fw-600 text-grey-900 font-xsss mb-0 ls-0">100% Secure Payments</h4>
                                        <p class="font-xsssss fw-500 text-grey-500 lh-26 mt-0 mb-0">100% Payment Protection.</p>
                                    </div>                 
                                    
                                    <div class="col-lg-3 col-md-6 xs-mb-4 md-mb-3 text-start">
                                        <i class="ti-headphone-alt text-grey-900 display1-size float-start me-3"></i>
                                        <h4 class="mt-0 fw-600 text-grey-900 font-xsss mb-0 ls-0">Support</h4>
                                        <p class="font-xsssss fw-500 text-grey-500 lh-26 mt-0 mb-0">Alway online feedback 24/7</p>
                                    </div>                 
                                    <div class="col-lg-3 col-md-6 md-mb-3 text-start">
                                        <i class="ti-lock text-grey-900 display1-size float-start me-3"></i>
                                        <h4 class="mt-0 fw-600 text-grey-900 font-xsss mb-0 ls-0">Trust pay</h4>
                                        <p class="font-xsssss fw-500 text-grey-500 lh-26 mt-0 mb-0">Easy Return Policy.</p>
                                    </div> 
                                    <div class="col-lg-3 col-md-6 text-start">
                                        <i class="ti-reload text-grey-900 display1-size float-start me-3"></i>
                                        <h4 class="mt-0 fw-600 text-grey-900 font-xsss mb-0 ls-0">Return and Refund</h4>
                                        <p class="font-xsssss fw-500 text-grey-500 lh-26 mt-0 mb-0">100% money back guarantee</p>
                                    </div>   
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- CATEGORY WRAPPER -->
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
                                            <img src="{product.picture}" alt="product-image" class="w-100 mt-3 mb-3 d-inline-block p-2 pt-0" style="width: 171px; height:138px;">
                                        </a>
                                        <div class="clearfix"></div>
                                        <h2 class="mt-2"><a href="single-product.html" class="text-grey-700 fw-600 font-xsss lh-22 d-block ls-0">{product.name}</a></h2>
                                        <h6 class="font-xss ls-3 fw-700 text-current d-flex">
                                            <span class="font-xsssss text-grey-500">₹</span>{product.price} <span class="ms-auto text-grey-500 fw-500 mt-1 font-xsssss"><select on:change={e => itemWeight = {id: product.id, weight: e.target.value}}>{@html product.weight_attributes}</select></span>
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
            </Route>
            <Route path="about">
                <div class="main-wrap pt-4">
                    <div class="container">
                        <div class="row">
                            <h3>About</h3>
                            <p>That's what it's all about!</p>
                        </div>
                    </div>
                </div>
            </Route>
            <!-- FOOTER WRAPPER -->
            <div class="lower-footer bg-white pb-3 pt-3">
                <div class="container">
                    <div class="row">
                        <div class="col-md-6 text-center text-sm-start xs-mb-3"><p class="text-grey-500 fw-500 font-xssss mb-0">@Copyright 2022 All rights reserved.</p></div>
                    </div>
                </div>
            </div>
            <!-- FOOTER WRAPPER -->
        </div>
        <div class="modal fade" id="checkoutproduct">
            <div class="modal-dialog modal-dialog-centered" style="max-width: 420px;">
                <div class="modal-content theme-dark-bg p-0 border-0">
                    <button type="button" class="btn-close z-index-5 bg-grey font-xsssss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-4 me-3" data-bs-dismiss="modal" aria-label="Close"></button>
                    <div class="modal-body vw100 text-start p-0 h-100">
                        <div class="card p-4 border-0 text-start h-100 ">
                            <h4 class="fw-700 font-lg text-grey-900 text-start mb-3 mt-n2 d-block">Shipping Address</h4>
                            <form on:submit|preventDefault={doSubmitOrder}>
                                <div class="row">
                                    <div class="col-lg-12 mb-3">
                                        <div class="form-group">
                                            <label class="mont-font fw-600 font-xssss mb-2 white-text">Name</label>
                                            <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.name} required>
                                        </div>        
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-lg-12 mb-3">
                                        <div class="form-group">
                                            <label class="mont-font fw-600 font-xssss mb-2 white-text">Mobile</label>
                                            <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.mobile} required>
                                        </div>
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-lg-12 mb-3">
                                        <div class="form-group">
                                            <label class="mont-font fw-600 font-xssss mb-2 white-text">Address</label>
                                            <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.address} required>
                                        </div>        
                                    </div>
                                    <div class="col-lg-12 mb-3">
                                        <div class="form-group">
                                            <label class="mont-font fw-600 font-xssss mb-2 white-text">Ward No</label>
                                            <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.ward_no} required>
                                        </div>        
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-lg-12 mb-3">
                                        <div class="form-group">
                                            <label class="mont-font fw-600 font-xssss mb-2 white-text">Pincode</label>
                                            <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.pincode} required>
                                        </div>        
                                    </div>
                                    <div class="col-lg-12">
                                        {orderMessage}
                                        <button type="submit" class="btn w-100 bg-current font-xsss ls-1 fw-600 text-white rounded-10 d-block text-center" disabled={orderCompleted}>Confirm Order</button>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- MENU MODAL -->
        <div class="modal fade left modal-scrollable" id="menumodal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content theme-dark-bg p-0 border-0 rounded-0">
                    <button type="button" class="btn-close z-index-5 bg-grey font-xsssss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-4 me-3" data-bs-dismiss="modal" aria-label="Close"></button>
                    <div class="modal-body vw100 text-start p-0 h-100">
                        <div class="card p-4 border-0 text-start h-100 ">
                            <h4 class="fw-700 font-lg text-grey-900 text-start mb-3 d-block ls-0"> Menu</h4>
                            <ul class="navbar-nav">
                                <li class="nav-item active dropdown">
                                    <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown" aria-expanded="false">Pages</a>
                                    <ul class="dropdown-menu border-0 shadow-xss">
                                        <li><a class="dropdown-item" href="#">Home</a></li>
                                        <li><a class="dropdown-item" href="#">About</a></li>
                                        <li><a class="dropdown-item" href="#">Terms and Conditions</a></li>
                                    </ul>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>              
        </div>
        <!-- CATEGORY MODAL -->

        <!-- CATEGORY MODAL -->
        <div class="modal fade left modal-scrollable" id="categorymodal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content theme-dark-bg p-0 border-0 rounded-0">
                    <button type="button" class="btn-close z-index-5 bg-grey font-xsssss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-3 me-3" data-bs-dismiss="modal" aria-label="Close"></button>
                    <div class="modal-body vw100 text-start p-0">
                        <div class="card p-4 border-0 text-start">
                            <h4 class="fw-700 font-lg text-grey-900 text-start mb-3 mt-n2 d-block ls-0"> Categories</h4>
                            <ul class="ms-0 ps-0 border-0 mt-3">
                                <li><a href="#" class="d-flex" data-bs-toggle="dropdown"> Beverages <i class="feather-chevron-right"></i></a>
                                    <ul class="submenu dropdown-menu pt-0 pb-0 ps-3">
                                        <li><a class="dropdown-item" href="#">Water</a></li>
                                        <li><a class="dropdown-item" href="#">Sparkling Water</a></li>
                                        <li><a class="dropdown-item" href="#">Soda & Pop</a></li>
                                        <li><a class="dropdown-item" href="#">Coffee</a></li>
                                        <li><a class="dropdown-item" href="#">Milk & Plant-Based Milk</a></li>
                                    </ul>
                                </li>
                                <li><a href="#" class="d-flex"> Biscuits & Snacks <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex"> Breads & Bakery <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex"> Breakfast & Dairy <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex"> Frozen Foods <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex"> Fruits & Vegetables <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex"> Grocery & Staples <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex"> Household Needs <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex"> Meats & Seafood <i class="feather-chevron-right"></i></a></li>

                                <li><a href="#" class="d-flex">Eggs Substitutes <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex">Honey Vegetables <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex">Marmalades Staples <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex">Sour Cream and Dips <i class="feather-chevron-right"></i></a></li>
                                <li><a href="#" class="d-flex">Yogurt Seafood <i class="feather-chevron-right"></i></a></li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>              
        </div>
        <!-- CATEGORY MODAL -->
        <!-- CART MODAL -->
        <div class="modal fade right modal-scrollable" id="cartmodal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" style="width: 350px;">
                <div class="modal-content theme-dark-bg p-0 border-0 rounded-0">
                    <button type="button" class="btn-close z-index-5 bg-grey font-xsssss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-3 me-3" data-bs-dismiss="modal" aria-label="Close"></button>
                    <div class="cart-box vh-100" >
                        <div class="modal-body vh-100 text-start p-0 d-flex align-items-start flex-column">
                            <div class="card w-100 p-4 pb-0 border-0 text-start">
                                <h4 class="fw-700 font-lg text-grey-900 text-start mb-3 mt-n2 d-block"> Cart</h4>
                                {#each cart as cartitem }
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
                                {#if cart.length > 0}
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
        <!-- CART MODAL -->

        <!-- SAVED MODAL -->
        <div class="modal fade right modal-scrollable" id="savedmodal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" style="width: 350px;">
                <div class="modal-content theme-dark-bg p-0 border-0 rounded-0">
                    <button type="button" class="btn-close z-index-5 bg-grey font-xsssss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-3 me-3" data-bs-dismiss="modal" aria-label="Close"></button>
                    <div class="cart-box vh-100" >
                        <div class="modal-body vh-100 text-start p-0 d-flex align-items-start flex-column">
                            <div class="card w-100 p-4 pb-0 border-0 text-start">
                                <h4 class="fw-700 font-lg text-grey-900 text-start mb-3 mt-n2 d-block"> Saved</h4>
                                <div class="row mb-3">
                                    <div class="col-md-5 col-xs-5"><a href="#" class="d-block text-center" data-bs-toggle="modal" data-bs-target="#productmodal"><img src="https://via.placeholder.com/171x148.png" alt="product-image" class="w-100 d-inline-block pt-3 pb-3 bg-greylight rounded-6"></a></div>
                                    <div class="col-md-7 col-xs-7 ps-0">
                                        <span class="ms-auto text-grey-500 fw-500 lh-1 font-xsssss mt-0 w-100 mb-2">500gm</span>
                                        <a href="single-product-1.html" class="text-grey-900 fw-600 font-xssss lh-22 d-block ls-0 mb-2">Blue Diamond Almonds Lightly Salted</a>
                                        <h6 class="font-xs ls-3 fw-700 text-current float-start mt-1"><span class="font-xsssss text-grey-500">$</span>29 </h6>
                                        <a href="#" class="text-uppercase font-xsssss text-grey-900 fw-700 ls-1 bg-greylight float-end w-125 lh-20 rounded-6 btn">Add to cart</a>
                                    </div>
                                </div>
                                <div class="row mb-3">
                                    <div class="col-md-5 col-xs-5"><a href="#" class="d-block text-center"><img src="https://via.placeholder.com/171x148.png" alt="product-image" class="w-100 d-inline-block pt-3 pb-3 bg-greylight rounded-6"></a></div>
                                    <div class="col-md-7 col-xs-7 ps-0">
                                        <span class="ms-auto text-grey-500 fw-500 lh-1 font-xsssss mt-0 w-100 mb-2">500gm</span>
                                        <a href="single-product-1.html" class="text-grey-900 fw-600 font-xssss lh-22 d-block ls-0 mb-2">Blue Diamond Almonds Lightly Salted</a>
                                        <h6 class="font-xs ls-3 fw-700 text-current float-start mt-1"><span class="font-xsssss text-grey-500">$</span>49 </h6>
                                        <a href="#" class="text-uppercase font-xsssss text-grey-900 fw-700 ls-1 bg-greylight float-end w-125 lh-20 rounded-6 btn">Add to cart</a>
                                    </div>
                                </div>
                                <div class="row mb-3">
                                    <div class="col-md-5 col-xs-5"><a href="#" class="d-block text-center"><img src="https://via.placeholder.com/171x148.png" alt="product-image" class="w-100 d-inline-block pt-3 pb-3 bg-greylight rounded-6"></a></div>
                                    <div class="col-md-7 col-xs-7 ps-0">
                                        <span class="ms-auto text-grey-500 fw-500 lh-1 font-xsssss mt-0 w-100 mb-2">100gm</span>
                                        <a href="single-product-1.html" class="text-grey-900 fw-600 font-xssss lh-22 d-block ls-0 mb-2">Blue Diamond Almonds Lightly Salted</a>
                                        <h6 class="font-xs ls-3 fw-700 text-current float-start mt-1"><span class="font-xsssss text-grey-500">$</span>99 </h6>
                                        <a href="#" class="text-uppercase font-xsssss text-grey-900 fw-700 ls-1 bg-greylight float-end w-125 lh-20 rounded-6 btn">Add to cart</a>
                                    </div>
                                </div>
                                <div class="row mb-3">
                                    <div class="col-md-5 col-xs-5"><a href="#" class="d-block text-center"><img src="https://via.placeholder.com/171x148.png" alt="product-image" class="w-100 d-inline-block pt-3 pb-3 bg-greylight rounded-6"></a></div>
                                    <div class="col-md-7 col-xs-7 ps-0">
                                        <span class="ms-auto text-grey-500 fw-500 lh-1 font-xsssss mt-0 w-100 mb-2">2Kg</span>
                                        <a href="single-product-1.html" class="text-grey-900 fw-600 font-xssss lh-22 d-block ls-0 mb-2">Blue Diamond Almonds Lightly Salted</a>
                                        <h6 class="font-xs ls-3 fw-700 text-current float-start mt-1"><span class="font-xsssss text-grey-500">$</span>39 </h6>
                                        <a href="#" class="text-uppercase font-xsssss text-grey-900 fw-700 ls-1 bg-greylight float-end w-125 lh-20 rounded-6 btn">Add to cart</a>
                                    </div>
                                </div>
                                <div class="row mb-3">
                                    <div class="col-md-5 col-xs-5"><a href="#" class="d-block text-center"><img src="https://via.placeholder.com/171x148.png" alt="product-image" class="w-100 d-inline-block pt-3 pb-3 bg-greylight rounded-6"></a></div>
                                    <div class="col-md-7 col-xs-7 ps-0">
                                        <span class="ms-auto text-grey-500 fw-500 lh-1 font-xsssss mt-0 w-100 mb-2">2Kg</span>
                                        <a href="single-product-1.html" class="text-grey-900 fw-600 font-xssss lh-22 d-block ls-0 mb-2">Blue Diamond Almonds Lightly Salted</a>
                                        <h6 class="font-xs ls-3 fw-700 text-current float-start mt-1"><span class="font-xsssss text-grey-500">$</span>39 </h6>
                                        <a href="#" class="text-uppercase font-xsssss text-grey-900 fw-700 ls-1 bg-greylight float-end w-125 lh-20 rounded-6 btn">Add to cart</a>
                                    </div>
                                </div>
                            </div>              
                            <div class="card w-100 p-4 pt-0 border-0 text-start mt-auto">
                                <a href="#" class="w-100 bg-current text-white rounded-6 text-center btn">Cart</a>
                            </div>      
                        </div>
                    </div>
                    
                </div>
            </div>              
        </div> 
        <!-- SAVED MODAL -->
        <!-- SINGLE PRODUCT MODAL -->
        <div class="modal fade right modal-scrollable" id="productmodal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" style="width: 350px;">
                <div class="modal-content theme-dark-bg border-0 rounded-0">
                    <button type="button" class="btn-close z-index-5 font-xsss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-0 me-0" data-bs-dismiss="modal" aria-label="Close"></button>
                    <div class="cart-box vh-100">
                        <div class="modal-body vh-100 text-start p-0 d-flex align-items-start flex-column p-4">
                            <div class="card border-0 w-100 text-center d-inline-block mb-3">
                                <span class="ls-3 font-xsssss text-white text-uppercase bg-current fw-700 p-2 d-inline-block rounded-3 posa left-15 top-15 z-index-5">30% off</span>
                                <div class="owl-carousel product-banner owl-theme overflow-hidden nav-none owl-dots-none owl-arrow-center">
                                    <div class="item me-0 bg-greylight text-center"><img src="https://via.placeholder.com/200x400.png" alt="product-image" class="shadow-none w-150 d-inline-block rounded-6 p-4"></div>
                                    <div class="item me-0 bg-greylight text-center"><img src="https://via.placeholder.com/200x400.png" alt="product-image" class="shadow-none w-150 d-inline-block rounded-6 p-4"></div>
                                </div>
                            </div>
                            <div class="card border-0 w-100 mt-2">
                                <div class="star d-inline text-left">
                                    <img src="images/star.png" alt="star" class="w-12 me-1 float-start">
                                    <img src="images/star.png" alt="star" class="w-12 me-1 float-start">
                                    <img src="images/star.png" alt="star" class="w-12 me-1 float-start">
                                    <img src="images/star.png" alt="star" class="w-12 me-1 float-start">
                                    <img src="images/star-disable.png" alt="star" class="w-12 me-2 float-start">
                                </div>
                                <h2 class="fw-700 text-grey-700 font-xss ls-0 lh-26 mt-2 mb-0 pe-lg-5">Blue Diamond Almonds Rice Orgomart Salted</h2>
                                <div class="cart-count cart-count-lg float-end mt-2">
                                    <h6 class="font-sm ls-3 fw-700 text-current float-start mt-1 me-4"><span class="font-xsssss text-grey-500">$</span>29 </h6>
                                    <div class="number">
                                        <span class="minus">-</span>
                                        <input type="text" class="open-font" value="1">
                                        <span class="plus">+</span>
                                    </div>
                                </div>
                                <p class="font-xssss fw-500 mt-3 text-grey-500">Vivamus adipiscing nisl ut dolor dignissim semper. Nulla luctus malesuada tincidunt. Class aptent taciti sociosqu ad litora torquent</p>
                                <h5 class="font-xssss fw-500 text-grey-500 mt-1"><b class="text-grey-700">Category:</b> Meats & Seafood </h5>
                                <h5 class="font-xssss fw-500 text-grey-500 mt-1"><b class="text-grey-700">Tags:</b> chicken, natural, Orgomart</h5>

                                <h5 class="font-xssss fw-500 text-grey-500 mt-3 d-flex"><i class="feather-bookmark font-xs text-current me-2 mt-n1"></i> <b class="text-grey-700 me-1">2 Month</b> Brand Warranty </h5>
                                <h5 class="font-xssss fw-500 text-grey-500 mt-2 d-flex"><i class="feather-help-circle font-xs text-current me-2 mt-n1"></i> <b class="text-grey-700 me-1">100% </b> Orgomart Product</h5>
                                <h5 class="font-xssss fw-500 text-grey-500 mt-2 d-flex mb-4"><i class="feather-alert-triangle font-xs text-current me-2 mt-n1"></i> <b class="text-grey-700 me-1">30 Days </b> Money back Return</h5>

                                <div class="alert-warning text-danger p-2 text-center w-100 font-xsssss fw-500 rounded-6">Covid-19 Info: We keep delivering.</div>
                                <a href="#" class="w-100 bg-current text-white rounded-6 text-center btn mt-2">ADD to Cart</a>
                                <a href="#" class="w-100 bg-black-08 text-white rounded-6 text-center btn mt-2 mb-3">ADD wishlist</a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div> 
    </main>
</Router>