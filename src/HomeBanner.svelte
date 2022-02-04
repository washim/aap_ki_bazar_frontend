<script>
    import Carousel from 'svelte-carousel';
</script>

<div class="banner-wrapper pt-4 pb-3 md-mt-6">
    <div class="container">
        <div class="row">
            <div class="col-lg-12">
                <div class="banner-wrap">
                    <Carousel let:showPrevPage let:showNextPage autoplay autoplayDuration={5000} autoplayProgressVisible pauseOnFocus>
                    <div slot="prev" on:click={showPrevPage} class="custom-arrow custom-arrow-prev badge badge-pill badge-primary">
                        <i class="feather-chevron-left"></i>
                    </div>
                    <div class="item">
                        <div class="video-responsive"><iframe src="https://www.youtube.com/embed/JGZolkleYiA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"></iframe></div>
                    </div>
                    <div class="item">
                        <div class="video-responsive"><iframe src="https://www.youtube.com/embed/H3WPMf4iqrs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"></iframe></div>
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