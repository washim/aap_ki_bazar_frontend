<script>
    import { cart } from './js/stores';

    $: total = $cart.reduce((sum, item) => sum + item.price * item.quantity, 0)
</script>

<div class="header-menu-mob pt-2 pb-2 shadow-xss position-fixed w-100 z-index-5 bg-white d-none d-block-md">
    <div class="container">
        <div class="row">
            <div class="col text-start"><button class="navbar-toggler border-0" type="button" data-bs-toggle="modal" data-bs-target="#menumodal"><span class="navbar-toggler-icon"></span></button></div>
            <div class="col text-center"><a href="#/"><div class="logo w-90"><i class="fas fa-pepper-hot"></i> App ki Bazar</div></a></div>
            <div class="col text-end"><a href="#/" class="nav-icon mt-1 d-inline-block" data-bs-toggle="modal" data-bs-target="#cartmodal"><i class="feather-shopping-bag text-grey-500 font-xl"></i> <span style="color: black; position:relative; top:-5px;">Cart Total: {#if total > 0 && total <= 130}₹{total+10}{:else}₹{total}{/if}</span></a></div>
        </div>
    </div>
</div>
<div class="upper-header pt-2 pb-2 bg-green d-none d-lg-block">
    <div class="container">
        <div class="row">
            <div class="col-lg-6">
                <ul class="nav">
                    <li class="nav-item"><a href="#/" class="ps-0">Home</a></li>
                </ul>
            </div>
            <div class="col-lg-6 text-end">
                <ul class="navbar-nav float-end">
                    <li class="nav-item">
                        <a class="nav-link" href="#/videos">Our Videos</a>
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
                <a href="#/"><div class="logo"><i class="fas fa-pepper-hot"></i> App ki Bazar</div></a>
                <div class="header-search ms-auto me-2 d-flex">
                    <a href="#" class="nav-icon" data-bs-toggle="modal" data-bs-target="#cartmodal"><i class="feather-shopping-bag text-grey-500"></i> <span style="color: white; position:relative; top:-5px;">Cart Total: {#if total > 0 && total <= 130}₹{total+10}{:else}₹{total}{/if}</span></a>
                </div>
            </div>
        </div>
    </div>
</div>