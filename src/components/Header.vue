<template>
    <div id="header">
        <div id="main-header">
            <ul class="list-inline">
                <li id="header-search">
                    <i class="fa fa-search"></i>
                    <input type="text" id="search-text" placeholder="search.." @keyup.enter="searchItem" v-model="searchInput">
                </li>
                <li id="header-name">SUPERMARKET</li>
                <ul id="header-account">
                    <li v-if="isLogged"><router-link to="/User">USER</router-link></li>
                    <li v-else><router-link to="/Login">LOGIN</router-link></li>
                    <li v-if="!isLogged"><router-link to="/Register">REGISTER</router-link></li>
                    <li><router-link to="/Cart"><i class="fa fa-shopping-cart"></i>CART</router-link></li>
                </ul>
            </ul>
        </div>
        <div id="bar-header">
            <div class="bar-header-base"></div>
            <ul class="list-inline">
                <router-link class="bar-header-button" to="/">HOME</router-link>
                <div class="list-dropdown">
                    <a class="bar-header-button">PRODUCTS</a>
                    <div class="dropdown-elem">
                        <a class="bar-header-button-selected cursor-default">PRODUCTS</a>
                        <div v-for="category in categories" :key="category.name">
                            <router-link class="bar-header-button" :to="'/ListItems/'+category.name">{{category.name}}</router-link>
                        </div>
                    </div>
                </div>
                <router-link class="bar-header-button" to="/AboutUs">ABOUT US</router-link>
                <router-link class="bar-header-button" to="/Contact"> CONTACT </router-link>
                <router-link v-if="isAdmin" class="bar-header-button" to="/Admin"> ADMIN </router-link>
            </ul>
        </div>
    </div>
</template>

<script>
import * as DB from '../dataSet/DatabaseConnector';
export default {
    name: 'Header',
    mounted: async function () {
        this.$root.$on('login', async (email) => {
            if (window.localStorage.getItem('currentUser') !== '') {
                window.localStorage.setItem('currentUser', email);
                this.isLogged = true;
                if (await DB.isAdmin()) {
                    this.isAdmin = true;
                }
            } else {
                this.isLogged = false;
                this.isAdmin = false;
            }
        });
        this.categories = await DB.getCategories();
    },
    data () {
        return {
            categories: {},
            isLogged: false,
            searchInput: '',
            isAdmin: false
        };
    },
    methods: {

        /**
         * Search item inputed using query in link
         */
        searchItem () {
            if (this.searchInput !== '' && this.$route.query.query !== this.searchInput) {
                this.$router.push('/ListItems?query=' + this.searchInput);
            }
        }
    }
};
</script>

<style>
    @import '../assets/style/style';
</style>
