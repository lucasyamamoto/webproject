<template>
    <div id="container-history">
        <div id="history-box" v-if="user !== null">
            <div id="history-sidebar">
                <router-link class="page-select" to="/User">User information</router-link>
                <a class="page-select active">Purchase history</a>
                <a class="page-select" @click="logout">Logout</a>
            </div>
            <div id="history-display" >
                <div class="loading" v-if="!isLoaded"></div>
                <div id="messageNoPurchase" v-else-if="noPurchaseError === true">
                    <p>No purchases made!</p>
                </div>
                <div v-else>
                    <div v-for="purchase in purchases" :key="purchase.price"  class='table-purchase'>
                        <h2>Data: {{purchase.date}}, {{purchase.time}}</h2>
                        <h2>Price: R${{purchase.price}}</h2>
                        <table>
                            <colgroup>
                                <col class="item">
                                <col class="amount">
                                <col class="total">
                            </colgroup>
                            <thead>
                                <th>Item</th>
                                <th>Price</th>
                                <th>Amount</th>
                                <th>Total</th>
                            </thead>
                            <tbody>
                                <tr v-for="item in purchase.cart" :key="item.product.name">
                                    <td>{{item.product.name}}</td>
                                    <td>{{item.product.price}}</td>
                                    <td>{{item.amount}}</td>
                                    <td>{{calculateTotal(item.product.price, item.amount)}}</td>
                                </tr>
                            </tbody>
                        </table>
                        <hr>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import * as DB from '../dataSet/DatabaseConnector';
import {calculateTotalProduct} from './shared';
export default {
    name: 'History',
    mixins: [calculateTotalProduct],
    mounted: async function () {
        try {
            this.user = await DB.getSession();
            this.purchases = await DB.getPurchases(this.user.email);
        } catch (err) {
            if (err.message === 'No purchases made') {
                this.noPurchaseError = true;
            } else {
                this.$router.push('/Login');
            }
        }
        this.isLoaded = true;
    },
    data () {
        return {
            user: null,
            purchases: null,
            noPurchaseError: false,
            isLoaded: false
        };
    },
    methods: {
        async logout () {
            await DB.logout();
            this.$root.$emit('login');
            this.$router.push('/');
        }
    }
};
</script>

<style scoped>
    @import '../assets/style/history';
</style>
