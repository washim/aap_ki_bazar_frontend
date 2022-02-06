<script>
    import { cart } from './js/stores';

    let customer = localStorage.getItem("shippingAddress") ? JSON.parse(localStorage.getItem("shippingAddress")) : {
        'name': '',
        'mobile': '',
        'address': '',
        'ward_no': '',
        'pincode': '',
        'weekly_day': '',
        'dbc': '',
        'created': '',
    };
    let orderCompleted = false;
    let orderMessage = '';
    
    async function doSubmitOrder() {
        if ($cart.length > 0) {
            const customer_data = JSON.stringify(customer);
            const create_shipping = await fetch('https://ecommerce.nbsols.com/api/shipping/', {
                method: 'POST',
                headers: {'Authorization': 'Basic YXBwX2tpX2JhemFyOkJhemFyQDc4NiM=', 'Content-Type': 'application/json'},
                body: customer_data
            });
            const shipping = await create_shipping.json()
            localStorage.setItem("shippingAddress", customer_data);
            if (shipping.id) {
                const create_invoice = await fetch('https://ecommerce.nbsols.com/api/invoices/', {
                    method: 'POST',
                    headers: {'Authorization': 'Basic YXBwX2tpX2JhemFyOkJhemFyQDc4NiM=', 'Content-Type': 'application/json'},
                    body: JSON.stringify({"status": "processed", "status_message": "Processed", "type": customer.weekly_day === "" ? "normal" : "weekly", "weekly_day": customer.weekly_day, "dbc": customer.dbc, "created": customer.created, "shipping": shipping.id})
                });
                const invoice = await create_invoice.json();
                if (invoice.id) {
                    for(let product of $cart) {
                        const create_order = await fetch('https://ecommerce.nbsols.com/api/orders/', {
                            method: 'POST',
                            headers: {'Authorization': 'Basic YXBwX2tpX2JhemFyOkJhemFyQDc4NiM=', 'Content-Type': 'application/json'},
                            body: JSON.stringify({"quantity": product.quantity, "weight": product.weight, "invoice": invoice.id, "product": product.id})
                        });
                    }
                }
                $cart = [];
                orderMessage = 'Your order successfully placed.';
                orderCompleted = true;
                setTimeout(() => {orderCompleted = false; orderMessage = '';}, 5000);
            }
        } else {
            orderMessage = 'Cart become empty. Choose products again for new order.';
        }
    }
</script>

<div class="modal fade" id="checkoutproduct">
    <div class="modal-dialog modal-dialog-centered" style="max-width: 420px;">
        <div class="modal-content theme-dark-bg p-0 border-0">
            <button type="button" class="btn-close z-index-5 bg-grey font-xsssss w-26 h-26 text-center rounded-circle posa right-0 top-0 mt-4 me-3" data-bs-dismiss="modal" aria-label="Close"></button>
            <div class="modal-body vw100 text-start p-0 h-100">
                <div class="card p-4 border-0 text-start h-100 ">
                    <h4 class="fw-700 font-lg text-grey-900 text-start mb-3 mt-n2 d-block">Shipping Address</h4>
                    <form on:submit|preventDefault={doSubmitOrder}>
                        <div class="row">
                            <div class="col-lg-12 col-12 mb-1">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Name</label>
                                    <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.name} required>
                                </div>        
                            </div>
                            <div class="col-lg-12 col-12 mb-1">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Mobile</label>
                                    <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.mobile} required>
                                </div>
                            </div>
                            <div class="col-lg-12 col-12 mb-1">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Address</label>
                                    <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.address} required>
                                </div>        
                            </div>
                            <div class="col-lg-6 col-6 mb-1">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Ward No</label>
                                    <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.ward_no} required>
                                </div>        
                            </div>
                            <div class="col-lg-6 col-6 mb-1">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Pincode</label>
                                    <input type="text" class="form-control theme-black-bg rounded-10" bind:value={customer.pincode} required>
                                </div>        
                            </div>
                            <div class="col-lg-6 col-6 mb-1">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Choose Delivery Date</label>
                                    <input type="datetime-local" class="form-control theme-black-bg rounded-10" bind:value={customer.created} required>
                                </div>        
                            </div>
                            <div class="col-lg-6 col-6 mb-1">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Activate weekly order</label>
                                    <select class="form-control theme-black-bg rounded-10" bind:value={customer.weekly_day}>
                                        <option value="">No. Weekly order not required</option>
                                        <option value="Monday">Every Monday</option>
                                        <option value="Tuesday">Every Tuesday</option>
                                        <option value="Wednesday">Every Wednesday</option>
                                        <option value="Thursday">Every Thursday</option>
                                        <option value="Friday">Every Friday</option>
                                        <option value="Saturday">Every Saturday</option>
                                        <option value="Sunday">Every Sunday</option>
                                    </select>
                                </div>        
                            </div>
                            <div class="col-lg-12 col-12 mb-3">
                                <div class="form-group">
                                    <label class="mont-font fw-600 font-xssss mb-2 white-text">Delivery Boy Code</label>
                                    <select class="form-control theme-black-bg rounded-10" bind:value={customer.dbc} required>
                                        <option></option>
                                        <option>DBC-01</option>
                                        <option>DBC-02</option>
                                        <option>DBC-03</option>
                                        <option>DBC-04</option>
                                        <option>DBC-05</option>
                                        <option>DBC-06</option>
                                        <option>DBC-07</option>
                                        <option>DBC-08</option>
                                        <option>DBC-09</option>
                                        <option>DBC-10</option>
                                    </select>
                                </div>        
                            </div>
                            <div class="col-lg-12 col-12">
                                {orderMessage}
                                <button type="submit" class="btn w-100 bg-current font-xsss ls-1 fw-600 text-white rounded-10 d-block text-center" disabled={orderCompleted}>Confirm Cash On Delivery</button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>