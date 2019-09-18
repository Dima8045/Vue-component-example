<template>
        <div class="container order-container" ref="orderWindow">
            <div class="order-window-desktop">
                <div class="order-window-title">ORDER SUMMARY</div>
                <div class="order-window">
                    <h3 class="title">{{title}}</h3>
                    <div class="item" v-if="isShowFlight">
                        <span class="item-title">Flight</span>
                        <div class="row">
                            <div class="col-7 item-first">KUL-SIN, 7/06/2019</div>
                            <div class="col-3 item-line"></div>
                            <div class="col-2 item-cnt">x{{travelers}}</div>
                        </div>
                        <div class="row">
                            <div class="col-7 item-first">SIN-KUL, 10/06/2019</div>
                            <div class="col-3 item-line"></div>
                            <div class="col-2 item-cnt">x{{travelers}}</div>
                        </div>
                    </div>
                    <div class="item" v-if="isShowHotel">
                        <span class="item-title">Hotel</span>
                        <div class="row">
                            <div class="col-7 item-first">4 Days 3 Nights, rooms, Hotel G Singapore, Singapore</div>
                            <div class="col-3 item-line"></div>
                            <div class="col-2 item-cnt">x{{rooms}}</div>
                        </div>
                    </div>
                    <div class="item">
                        <span class="item-title">Event Tickets</span>
                        <div class="row">
                            <div class="col-7 item-first">PGA | 2-Day Ticket</div>
                            <div class="col-3 item-line"></div>
                            <div class="col-2 item-cnt">x{{travelers}}</div>
                        </div>
                    </div>
                    <div class="section-total">
                        <div class="title">TOTAL</div>
                        <div class="price">SGD {{totalAmount}}</div>
                    </div>
                    <div class="btn-checkout-section">
                        <button type="button" class="btn btn-primary btn-checkout" @click="buyTickets">
                            Checkout
                        </button>
                    </div>
                </div>
            </div>
            <div id="section-total-mobile-pre"></div>
            <div class="order-window-mobile" id="section-total-mobile">
                <div class="section-total"> 
                    <div><span class="title">Total:</span> <span class="price">SGD {{totalAmount}} </span></div>
                    <button type="button" class="btn btn-primary btn-checkout" @click="buyTickets">
                        Continue
                    </button>
                </div>
            </div>
        </div>
</template>

<script>
    import { EventBus } from '../event.js';
    export default {
        name: 'PackageOrderSummary',
        props: {
            type: String,
            title: String
        },
        data () {
            return {
                travelers:1,
                rooms:1,
                ticketPrice:248,
                hotelPrice:405,
                flightPrice:180,
                marginPackageHotel:14,
                marginPackageFlight:10,
                marginPackageHotelFlight:18,
                packs:[]
            }
        },
        computed: {
            isShowFlight: function () {
                return 'hotel' != this.type;
            },
            isShowHotel: function () {
                return 'flight' != this.type;
            },
            totalAmount: function () {
                if(!this.travelers || !this.rooms)
                    return 0;
                let total=this.travelers*this.ticketPrice
                switch(this.type) {
                    case 'hotel':
                        total+=this.rooms*this.hotelPrice
                        total+=this.marginPackageHotel
                        break;
                    case 'flight':
                        total+=this.travelers*this.flightPrice
                        total+=this.marginPackageFlight
                        break;
                    default:
                        total+=this.travelers*this.flightPrice
                        total+=this.rooms*this.hotelPrice
                        total+=this.marginPackageHotelFlight
                        break;
                }

                return total.toLocaleString("en-EN",{minimumFractionDigits: 2, maximumFractionDigits: 2});
            },
            isMobile: function () {
                if(!this.$refs.orderWindow)
                    return false;
                return this.$refs.orderWindow.clientWidth<820;
            }
        },
        methods: {
            buyTickets () {
                let url='https://tickets.airasiaredtix.com/airasia-redtix/ultrasingapore2019pack'
                let r=('flight'==this.type)?0:this.rooms;
                let key=this.type+'-t'+this.travelers+'-r'+r

                url+=this.packs[key]+'/booking'
                window.open(url, '_blank');
            },
            handleScroll (event) {
                if($('#section-total-mobile-pre').offset()&&$('#section-total-mobile-pre').offset().top>=(window.scrollY+window.innerHeight-70)) {
                    $('#section-total-mobile').addClass('order-window-mobile-fixed');
                }else {
                    $('#section-total-mobile').removeClass('order-window-mobile-fixed');
                }
            }
        },
        mounted () {
            EventBus.$on('searchChanged', data => {
                if(!data)
                    return;
                this.travelers=data['travelers'];
                this.rooms=data['rooms'];
            })
        },
        beforeMount () {
            //[package-type-travellers-rooms]
            this.packs['flight-hotel-t1-r1']=1
            this.packs['flight-hotel-t2-r1']=2
            this.packs['flight-hotel-t2-r2']=3
            this.packs['flight-hotel-t3-r2']=4
            this.packs['flight-hotel-t3-r3']=5
            this.packs['flight-hotel-t4-r2']=6
            this.packs['flight-hotel-t4-r3']=7
            this.packs['flight-hotel-t4-r4']=8
            this.packs['hotel-t1-r1']=9
            this.packs['hotel-t2-r1']=10
            this.packs['hotel-t2-r2']=11
            this.packs['hotel-t3-r2']=12
            this.packs['hotel-t3-r3']=13
            this.packs['hotel-t4-r2']=14
            this.packs['hotel-t4-r3']=15
            this.packs['hotel-t4-r4']=16
            this.packs['flight-t1-r0']=17
            this.packs['flight-t2-r0']=18
            this.packs['flight-t3-r0']=19
            this.packs['flight-t4-r0']=20
        },
        created () {
            window.addEventListener('scroll', this.handleScroll);
        },
        destroyed () {
            window.removeEventListener('scroll', this.handleScroll);
        }
    }
</script>